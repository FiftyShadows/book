安装特别头疼,https://github.com/ethereum/mist ,看这个文档很多坑

#### 安装依赖
```shell
$ curl https://install.meteor.com/ | sh
$ sudo npm install -g electron --unsafe-perm=true --allow-root --registry=https://registry.npm.taobao.org
$ npm install -g gulp`
```
如果 timeout
`vim ~/.npmrc`,再加入
```python
registry=https://registry.npm.taobao.org
sass_binary_site=https://npm.taobao.org/mirrors/node-sass/
phantomjs_cdnurl=http://npm.taobao.org/mirrors/phantomjs
ELECTRON_MIRROR=http://npm.taobao.org/mirrors/electron/
```
#### 安装Mist本体

```
$ git clone https://github.com/ethereum/mist.git
$ cd mist
$ git submodule update --init
$ yarn
```
####运行Mist/interface 也就是mist的后台程序
`cd mist/interface && meteor --no-release-check`