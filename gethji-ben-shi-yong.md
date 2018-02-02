 mac下安装
 
 > brew tap ethereum/ethereum
brew install ethereum

安装完后`geth version1`检查下.


建立一个文件genesis.json,填入

```json
{
  "nonce": "0x0000000000000042",
  "timestamp": "0x0",
  "parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
  "extraData": "0x0",
  "gasLimit": "0x80000000",
  "difficulty": "0x1",
  "mixhash": "0x0000000000000000000000000000000000000000000000000000000000000000",
  "coinbase": "0x3333333333333333333333333333333333333333",
  "alloc": {     }
}
```

在当前文件夹下
`geth --datadir "./" init genesis.json`
- 这时候可以注意一下，此时当前目录下面会新增出两个文件夹geth和keystore

geth中保存的是区块链的相关数据
keystore中保存的是该链条中的用户信息.



执行一条最简单的geth命令，来创建自己的私有链条
`geth --datadir "./" --nodiscover console 2>>geth.log`

进入命令行模式，其中参数

– datadir 代表文件夹地址，
– nodiscover 代表该链条不希望被其他节点发现，
console >> geth.log 代表将控制台输出到文件geth.log中去
当然从命令行模式退出，也很简单，只要打入exit, 即可退出