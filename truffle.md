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
```
➜  truffle-test truffle init
Downloading...
Unpacking...
Setting up...
Unbox successful. Sweet!

Commands:

  Compile:        truffle compile
  Migrate:        truffle migrate
  Test contracts: truffle test
➜  truffle-test truffle compile
Error: Could not find /Users/admin/Desktop/truffle-test/contracts/Hello_mshk_top.sol from any sources
    at /usr/local/lib/node_modules/truffle/build/webpack:/~/truffle-resolver/index.js:76:1
    at /usr/local/lib/node_modules/truffle/build/webpack:/~/async/internal/onlyOnce.js:12:1
    at next (/usr/local/lib/node_modules/truffle/build/webpack:/~/async/whilst.js:68:1)
    at /usr/local/lib/node_modules/truffle/build/webpack:/~/truffle-resolver/index.js:64:1
    at /usr/local/lib/node_modules/truffle/build/webpack:/~/truffle-resolver/fs.js:65:1
    at /usr/local/lib/node_modules/truffle/build/webpack:/~/async/internal/once.js:12:1
    at replenish (/usr/local/lib/node_modules/truffle/build/webpack:/~/async/internal/eachOfLimit.js:59:1)
    at iterateeCallback (/usr/local/lib/node_modules/truffle/build/webpack:/~/async/internal/eachOfLimit.js:49:1)
    at /usr/local/lib/node_modules/truffle/build/webpack:/~/async/internal/onlyOnce.js:12:1
    at ReadFileContext.callback (/usr/local/lib/node_modules/truffle/build/webpack:/~/truffle-resolver/fs.js:61:1)
➜  truffle-test truffle compile
Compiling ./contracts/Hello_mshk_top.sol...
Compiling ./contracts/Migrations.sol...
Writing artifacts to ./build/contracts


➜  truffle-test truffle migrate
Using network 'development'.

Running migration: 1_initial_migration.js
  Deploying Migrations...
  ... 0xc6870cfac7a10d482daf09407316bd1931631f71b2554a5c483f6f1e373d10cd
  Migrations: 0x8cdaf0cd259887258bc13a92c0a6da92698644c0
Saving successful migration to network...
  ... 0xd7bc86d31bee32fa3988f1c1eabce403a1b5d570340a3a9cdba53a472ee8c956
Saving artifacts...
Running migration: 2_deploy.js
  Deploying Hello_mshk_top...
  ... 0x2d4d8dea45ebe2c9ca0ebce9eaef5240b5c3ed4f30cd66416f2eac4ed16a60ab
  Hello_mshk_top: 0x345ca3e014aaf5dca488057592ee47305d9b3e10
Saving successful migration to network...
  ... 0xf36163615f41ef7ed8f4a8f192149a0bf633fe1a2398ce001bf44c43dc7bdda0
Saving artifacts...
➜  truffle-test
```
合约里新建一个 HelloWorld.sol 文件
```js
pragma solidity ^0.4.17;

contract HelloWorld {

  //say hello mshk.top
  function say() public pure returns (string) {
    return "Hello World";
  }

  //print name
  function print(string name) public pure returns (string) {
    return name;
  }
}
```
新建一个2_deploy.js
```js
var Hello_mshk_top = artifacts.require("./HelloWorld.sol");
module.exports = function(deployer) {
  deployer.deploy(Hello_mshk_top);
};
```
修改truffle.js
```js
module.exports = {
    networks: {
        development: {
          host: "localhost",
          port:7545,
          network_id: "*"
        }
    }
};
```
新打开命令行,进入项目目录,输入` truffle console`

```js
truffle(development)> var contract;
undefined
truffle(development)> HellWorld.deployed().then(function(instance){contract= instance;});
undefined
truffle(development)> contract.say()
'HelloWorld'
truffle(development)> contract.print("aaa")
'aaa'
```