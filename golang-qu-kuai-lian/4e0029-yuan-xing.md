
####1. 区块(Block)
区块链中的取款存储着有价值的信息.讲个例子,比特币的区块存储对于任何加密货币都是核心的交易记录.之外,一个区块还包含技术信息,比如版本号,当前的时间戳和上一个区块的哈希值.

以下是简化版的区块

```go
package main

type Block struct{
	Timestamp int64  //当前时间戳(区块被创建的时间)
	Data [] byte     //区块中实际包含的有用信息
	PrevBlockHash [] byte //存储的前一个区块的哈希值
	Hash [] byte  //当前区块的哈希值
}
```

在比特币规范当中，Timestamp，PrevBlockHash，Hash是区块头部数据（block headers），自成一个单独的数据结构，而交易记录（Transactions，在我们这个版本当中就是Data）构成另外一个单独的数据结构。为了简化，这里我们将两者混合到一个数据结构当中。

接下来如何计算哈希,哈希值的计算是区块链的重要特性,哈希值需要很多计算(计算能力更强的 gpu 挖矿的原因),目的是让区块链中增加新的区块链变难,确保一旦新的区块成功加入很难被篡改.

我们只需将区块中的各个值关联起来,计算出一个 [SHA-256](https://zh.wikipedia.org/wiki/SHA-2) 哈希,我们在 SetHash() 函数中实现哈希值的计算, SetHash() 会调用 bytes 包和 sha256包的函数.
```go
func (b *Block) SetHash() {
	timestamp := []byte(strconv.FormatInt(b.Timestamp, 10))
	headers := bytes.Join([][]byte{b.PrevBlockHash, b.Data, timestamp}, []byte{})
	hash := sha256.Sum256(headers)

	b.Hash = hash[:]
}
```
然后按照Go语言的惯例，实现一个简化创建区块的函数，代码如下：

```go
func NewBlock(data string, prevBlockHash []byte) *Block {
	block := &Block{time.Now().Unix(), []byte(data), prevBlockHash, []byte{}}
	block.SetHash()
	return block
}
```

####2. 区块链(Blockchain)
实现一个区块链,本质上,区块链是带有特定数据结构的数据库,是一个有序的,反向链接的列表( back-linked list). 这意味着区块是按照插入的顺序存储的,并且每个区块都连接到前面一个区块,这种结构允许快速获取最新的区块.


go 中可以使用 array 和 map 实现,数据用来存储有序哈希(go 中的数组是有序的),然后用 map 存储哈希( hash:block),map 是无序的.

```go
type Blockchain struct{
	blocks [] *Block
}
```

接下来往区块链里添加区块
```go
func (bc *Blockchain) AddBlock(data string) {
	prevBlock := bc.blocks[len(bc.blocks)-1]
	newBlock := NewBlock(data, prevBlock.Hash)
	bc.blocks = append(bc.blocks, newBlock)
}
```

增加一个区块需要一个已经存在的区块,最早的这个区块叫创始块(Genesis Block). 来创造一个创世块

```go
func NewGenesisBlock() *Block {
	return NewBlock("Genesis Block", []byte{})
}
```

实现一个用创世块创建一个区块链的函数

```go
func NewBlockchain() *Blockchain {
	return &Blockchain{[]*Block{NewGenesisBlock()}}
}
```

测试一下能否工作

```go
func main() {
	bc := NewBlockchain()

	bc.AddBlock("Send 1 BTC to Ivan")
	bc.AddBlock("Send 2 more BTC to Ivan")

	for _, block := range bc.blocks {
		fmt.Printf("Prev. hash: %x\n", block.PrevBlockHash)
		fmt.Printf("Data: %s\n", block.Data)
		fmt.Printf("Hash: %x\n", block.Hash)
		fmt.Println()
	}
}
```

输出：

```
Prev. hash:
Data: Genesis Block
Hash: aff955a50dc6cd2abfe81b8849eab15f99ed1dc333d38487024223b5fe0f1168

Prev. hash: aff955a50dc6cd2abfe81b8849eab15f99ed1dc333d38487024223b5fe0f1168
Data: Send 1 BTC to Ivan
Hash: d75ce22a840abb9b4e8fc3b60767c4ba3f46a0432d3ea15b71aef9fde6a314e1

Prev. hash: d75ce22a840abb9b4e8fc3b60767c4ba3f46a0432d3ea15b71aef9fde6a314e1
Data: Send 2 more BTC to Ivan
Hash: 561237522bb7fcfbccbc6fe0e98bbbde7427ffe01c6fb223f7562288ca2295d1
```
####3. 结论

我们创建了一个极简的区块链原型：它只不过是一个由区块构成的数组，每个区块都链接指向上一个区块。当然，真正的区块链远比这个复杂的多。在我们的区块链里面添加一个区块又快又容易，然后在实际的区块链当中添加一个区块着实需要一些工作：在获得增加区块的允许之前要做很繁重的计算才行（这个机制被称之为“工作证明机制”，即“Proof-of-Work”，POW）。并且，区块链是一个没有单一决策者的分布式数据库。因此，任何一个区块被加入之前都需要得到网络中其他的参与和的确认和批准（这个机制被称之为“共识机制”，即“Consenus”），还有，在我们的区块链里面还没有任何交易记录呢！



