 mac下安装
 
 > brew tap ethereum/ethereum
brew install ethereum

安装完后`geth version`检查下.


建立一个文件genesis.json,填入

```json
{
    "config": {
        "chainId": 15,
        "homesteadBlock": 0,
        "eip155Block": 0,
        "eip158Block": 0
    },
    "coinbase" : "0x0000000000000000000000000000000000000000",
    "difficulty" : "0x40000",
    "extraData" : "",
    "gasLimit" : "0xffffffff",
    "nonce" : "0x0000000000000042",
    "mixhash" : "0x0000000000000000000000000000000000000000000000000000000000000000",
    "parentHash" : "0x0000000000000000000000000000000000000000000000000000000000000000",
    "timestamp" : "0x00",
    "alloc": { }
}

```
![](/assets/WX20180205-102701@2x.png)

在当前文件夹下
`geth --datadir "./" init genesis.json`
- 这时候可以注意一下，此时当前目录下面会新增出两个文件夹geth和keystore

geth中保存的是区块链的相关数据
keystore中保存的是该链条中的用户信息.



执行一条最简单的geth命令，来创建自己的私有链条
`geth --datadir "./" --nodiscover console 2>>geth.log`
详细点的就是`geth --datadir "./" --nodiscover --rpc --rpcaddr 127.0.0.1 --rpcport 7050 --rpcapi "db,eth,net,web3,miner,personal"  --networkid 10   console 2>>eth_log.log`

进入命令行模式，其中参数

identity

- 区块链的标示，随便填写，用于标示目前网络的名字

init

- 指定创世块文件的位置，并创建初始块

datadir

- 设置当前区块链网络数据存放的位置

port

- 网络监听端口

rpc

- 启动rpc通信，可以进行智能合约的部署和调试

rpcapi

- 设置允许连接的rpc的客户端，一般为db,eth,net,web3

networkid

- 设置当前区块链的网络ID，用于区分不同的网络，是一个数字

console

- 启动命令行模式，可以在Geth中执行命令
当然从命令行模式退出，也很简单，只要打入exit, 即可退出

再新打开一个命令行,输入`tail -f geth.log`,用来看日志


`eth.accounts`发现没账户
`personal.newAccount("123456")`创建一个
`miner.start()`开始挖矿,返回 true, 返回 null 别慌,看日志,会显示正在挖矿,这是个版本的坑
`miner.stop()`停止挖矿,返回 true
`eth.getBalance(eth.accounts[0])`查看余额

<br>
##### 转账

一定要记得要正在挖矿中才能交易

再新建一个账户,步骤跟上面一样
```shell
> acc0 = eth.accounts[0]
"0xbcf5b841303bc08026ce2d3b8f83498ffe42c12f"
> acc1 = eth.accounts[1]
"0xb8b12a801b610176935a15321f77b48dd5c0c448"
> amount = web3.toWei(0.01)
"10000000000000000"
```
再执行`eth.sendTransaction({from: acc0, to: acc1, value: amount})`
会报错,账户没解锁.` personal.unlockAccount(acc0)`执行这句.