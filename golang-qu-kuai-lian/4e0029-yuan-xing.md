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
