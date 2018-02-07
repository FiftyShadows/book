-  开发场景一般用`EtherumJS TestRPC`
- 生产环境一般用`Geth (go-ethereum)`

安装 truffle `npm install -g truffle` 查看`truffle version`

安装 TestRPC `npm install -g ethereumjs-testrpc`

#### 基本使用
```
➜  ~ cd desktop
➜  desktop mkdir test_truffle
➜  desktop cd test_truffle
➜  test_truffle truffle init
Downloading...
Unpacking...
Setting up...
Unbox successful. Sweet!

Commands:

  Compile:        truffle compile
  Migrate:        truffle migrate
  Test contracts: truffle test
```
这将会是你的目录
![](/assets/QQ20180207-161923@2x.png)
-  contracts 智能合约目录
-  migrations 发布脚本目录
- test 存放测试文件
- truffle.js Truffle的配置文件

编译命令 `truffle compile` ,仅默认编译自上次编译后被修改过的文件，来减少不必要的编译。如果你想编译全部文件，可以使用`truffle compile --compile-all`选项

Truffle需要定义的合约名称和文件名准确匹配,这种匹配是区分大小写的，也就是说大小写也要一致。推荐大写每一个开头字母。

　　文件之间的相互依赖，可以使用import进行合约间的引用，Truffle将会按正确顺序依次编译合约，并在需要的时候自动关联库。例如：`import "./AnotherContract.sol";`

#### hello world

