####先安装`npm install truffle-hdwallet-provider --save`

####再更改 truffle.js 配置
```js
var HDWalletProvider = require("truffle-hdwallet-provider");


//https://infura.io/   去这里拿 apikey

//这是我申请好的https://ropsten.infura.io/eoXrf4B9tiVha0RyUh9W 
var infura_apikey = "XXXXXX";
//助记词
var mnemonic = "twelve words you can find in metamask/settings/reveal seed words blabla";

module.exports = {
  networks: {
    development: {
      host: "localhost",
      port: 8545,
      network_id: "*" // Match any network id
    },
    ropsten: {
      provider: new HDWalletProvider(mnemonic, "https://ropsten.infura.io/"+infura_apikey),
      network_id: 3
    }
  }
};
```
####部署的时候最好翻墙
####然后再执行这个命令
`sudo rm -rf build && sudo truffle migrate --rest --network ropsten`

####去这个网站  https://ropsten.etherscan.io/
![](/assets/QQ20180326-180007@2x.png)


![](/assets/QQ20180326-180105@2x.png)

![](/assets/QQ20180326-180301@2x.png)