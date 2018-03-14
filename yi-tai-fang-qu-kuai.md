以太坊区块由Header和Body两部分组成。
 
　　其中Header部分成员如下：
　　ParentHash，父区块哈希
　　UncleHash，叔区块哈希，具体为Body中Uncles数组的RLP哈希值。RLP哈希，即某类型对象RLP编码后做SHA3哈希运算。
　　Coinbase，矿工地址。
　　Root，StateDB中state Trie根节点RLP哈希值。
　　TxHash，Block中tx Trie根节点RLP哈希值。
　　ReceiptHash，Block中Receipt Trie根节点的RLP哈希值。
　　Difficulty，区块难度，即当前挖矿难度。
　　Number，区块序号，即父区块Number+1。
　　GasLimit，区块内所有Gas消耗的理论上限，创建时指定，由父区块GasUsed和GasLimit计算得出。
　　GasUsed，区块内所有Transaction执行时消耗的Gas总和。
　　Time，当前时间戳。
　　Nonce，随机数Nonce值。
 
　　有关叔区块：
　　叔区块，即孤立的块。以太坊成块速度较快，导致产生孤块。
　　以太坊会给发现孤块的矿工以回报，激励矿工在新块中引用孤块，引用孤块使主链更重。在以太坊中，主链是指最重的链。
 
　　有关state Trie、tx Trie和Receipt Trie：
　　state Trie，所有账户对象可以逐个插入一个Merkle-PatricaTrie(MPT)结构中，形成state Trie。
　　tx Trie：Block中Transactions中所有tx对象，逐个插入MPT结构中，形成tx Trie。
　　Receipt Trie：Block中所有Transaction执行后生成Receipt数组，所有Receipt逐个插入MPT结构中，形成Receipt Trie。
 
　　Body成员如下：
　　Transactions，交易列表。
　　Uncles，引用的叔区块列表。
 
　　go-ethereum-1.7.3源码中区块头和区块定义：
 
 ```go
 type Header struct {
    //父区块哈希
    ParentHash  common.Hash
    //叔区块哈希
    UncleHash   common.Hash
    //矿工地址
    Coinbase    common.Address
    //StateDB中state Trie根节点RLP哈希值
    Root        common.Hash
    //Block中tx Trie根节点RLP哈希值
    TxHash      common.Hash
    //Block中Receipt Trie根节点的RLP哈希值
    ReceiptHash common.Hash
    Bloom       Bloom
    //区块难度
    Difficulty  *big.Int
    //区块序号
    Number      *big.Int
    //区块内所有Gas消耗的理论上限
    GasLimit    *big.Int
    //区块内所有Transaction执行时消耗的Gas总和
    GasUsed     *big.Int
    //当前时间戳
    Time        *big.Int
    Extra       []byte
    MixDigest   common.Hash
    //随机数Nonce值
    Nonce       BlockNonce
}

type Body struct {
    //交易列表
    Transactions []*Transaction
    //引用的叔区块列表
    Uncles       []*Header
}
//代码位置core/types/block.go
```

###以太坊Pow算法原理
 
　　以太坊Pow算法可以表示为如下公式：
　　RAND(h, n) <= M / d
 
　　其中RAND()表示一个概念函数，代表一系列的复杂运算。
　　其中h和n为输入，即区块Header的哈希、以及Header中的Nonce。
　　M表示一个极大的数，此处使用2^256-1。
　　d，为区块难度，即Header中的Difficulty。
 
　　因此在h和n确定的情况下，d越大，挖矿难度越大，即为Difficulty本义。
　　即不断变更Nonce，使RAND(h, n)满足RAND(h, n) <= M / d，即完成Pow。
 
go-ethereum-1.7.3源码中Pow算法实现：
```go
func (ethash *Ethash) mine(block *types.Block, id int, seed uint64, abort chan struct{}, found chan *types.Block) {
    // Extract some data from the header
    var (
        header = block.Header()
        hash   = header.HashNoNonce().Bytes()
        //target，即M / d，即(2^256-1)/Difficulty
        target = new(big.Int).Div(maxUint256, header.Difficulty)

        number  = header.Number.Uint64()
        dataset = ethash.dataset(number)
    )
    // Start generating random nonces until we abort or find a good one
    var (
        attempts = int64(0)
        nonce    = seed
    )
    logger := log.New("miner", id)
    logger.Trace("Started ethash search for new nonces", "seed", seed)
    for {
        select {
        case <-abort:
            // Mining terminated, update stats and abort
            logger.Trace("Ethash nonce search aborted", "attempts", nonce-seed)
            ethash.hashrate.Mark(attempts)
            return

        default:
            // We don't have to update hash rate on every nonce, so update after after 2^X nonces
            attempts++
            if (attempts % (1 << 15)) == 0 {
                ethash.hashrate.Mark(attempts)
                attempts = 0
            }
            //hashimotoFull即RAND(h, n)所代表的一系列的复杂运算
            digest, result := hashimotoFull(dataset, hash, nonce)
            //result满足RAND(h, n)  <=  M / d
            if new(big.Int).SetBytes(result).Cmp(target) <= 0 {
                // Correct nonce found, create a new header with it
                header = types.CopyHeader(header)
                header.Nonce = types.EncodeNonce(nonce)
                header.MixDigest = common.BytesToHash(digest)

                // Seal and return a block (if still needed)
                select {
                case found <- block.WithSeal(header):
                    logger.Trace("Ethash nonce found and reported", "attempts", nonce-seed, "nonce", nonce)
                case <-abort:
                    logger.Trace("Ethash nonce found but discarded", "attempts", nonce-seed, "nonce", nonce)
                }
                return
            }
            //不断变更Nonce
            nonce++
        }
    }
}
//代码位置consensus/ethash/sealer.go
```

###以太坊挖矿难度计算
以太坊每次挖矿均需计算当前区块难度。
　　按版本不同有三种计算难度的规则，分别为：calcDifficultyByzantium（Byzantium版）、calcDifficultyHomestead（Homestead版）、calcDifficultyFrontier（Frontier版）。此处以calcDifficultyHomestead为例。
 
　　计算难度时输入有：
　　parent_timestamp：父区块时间戳
　　parent_diff：父区块难度
　　block_timestamp：当前区块时间戳
　　block_number：当前区块的序号
 
　　当前区块难度计算公式，即：


```go
block_diff = parent_diff
+ (parent_diff / 2048 * max(1 - (block_timestamp - parent_timestamp) // 10, -99)
+ 2^((block_number // 100000) - 2)
```

　其中//为整数除法运算符，a//b，即先计算a/b，然后取不大于a/b的最大整数。

调整难度的目的，即为使挖矿时间保持在10-19s期间内，如果低于10s增大挖矿难度，如果大于19s将减小难度。另外，计算出的当前区块难度不应低于以太坊创世区块难度，即131072。

go-ethereum-1.7.3源码中计算挖矿难度代码如下：
 ```go
 func calcDifficultyHomestead(time uint64, parent *types.Header) *big.Int {
    // https://github.com/ethereum/EIPs/blob/master/EIPS/eip-2.mediawiki
    // algorithm:
    // diff = (parent_diff +
    //         (parent_diff / 2048 * max(1 - (block_timestamp - parent_timestamp) // 10, -99))
    //        ) + 2^(periodCount - 2)

    bigTime := new(big.Int).SetUint64(time)
    bigParentTime := new(big.Int).Set(parent.Time)

    // holds intermediate values to make the algo easier to read & audit
    x := new(big.Int)
    y := new(big.Int)

    // 1 - (block_timestamp - parent_timestamp) // 10
    x.Sub(bigTime, bigParentTime)
    x.Div(x, big10)
    x.Sub(big1, x)

    // max(1 - (block_timestamp - parent_timestamp) // 10, -99)
    if x.Cmp(bigMinus99) < 0 {
        x.Set(bigMinus99)
    }
    // (parent_diff + parent_diff // 2048 * max(1 - (block_timestamp - parent_timestamp) // 10, -99))
    y.Div(parent.Difficulty, params.DifficultyBoundDivisor)
    x.Mul(y, x)
    x.Add(parent.Difficulty, x)

    // minimum difficulty can ever be (before exponential factor)
    if x.Cmp(params.MinimumDifficulty) < 0 {
        x.Set(params.MinimumDifficulty)
    }
    // for the exponential factor
    periodCount := new(big.Int).Add(parent.Number, big1)
    periodCount.Div(periodCount, expDiffPeriod)

    // the exponential factor, commonly referred to as "the bomb"
    // diff = diff + 2^(periodCount - 2)
    if periodCount.Cmp(big1) > 0 {
        y.Sub(periodCount, big2)
        y.Exp(big2, y, nil)
        x.Add(x, y)
    }
    return x
}
//代码位置consensus/ethash/consensus.go
```

Pow算法概念简单，即工作端提交难以计算但易于验证的计算结果，其他节点通过验证这个结果来确信工作端完成了相当的工作量。
　　但其缺陷也很明显：1、随着节点将CPU挖矿升级为GPU、甚至矿机挖矿，节点数和算力已渐渐失衡；2、比特币等网络每秒需完成数百万亿次哈希计算，资源大量浪费。
　　为此，业内提出了Pow的替代者如PoS权益证明算法，即要求用户拥有一定数量的货币，才有权参与确定下一个合法区块。另外，相对拥有51%算力，购买超过半数以上的货币难度更大，也使得恶意攻击更加困难。
