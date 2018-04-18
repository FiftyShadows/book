拉去 docker 的 mysql 镜像
`docker pull mysql`
再
`docker run --name test-mysql -p 3308:3306  -e MYSQL_ROOT_PASSWORD=123456 -d mysql`
进入 mysql终端
`docker exec -it test-mysql mysql -uroot -p123456`
进入之后，要对用户进行授权，否则用navicat连接不上
```
GRANT ALL PRIVILEGES ON *.* TO 'root'@'localhost' IDENTIFIED BY '123456';
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY '123456';
GRANT ALL PRIVILEGES ON *.* TO 'root'@'192.168.12.5' IDENTIFIED BY '123456';
```


例如：docker run --name test-mysql -p 3308:3306 -e MYSQL_ROOT_PASSWORD=password -dmysql/mysql-server:latest

含义：容器的名字为test-mysql，将容器的3308端口映射到本机的3306端口，mysql数据库的密码为password ，运行的镜像为mysql/mysql-server:latest


####大坑注意`docker run --name mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=rootroot -d mysql` 基本失败
```
➜  Seckill docker run --name mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=rootroot -d mysql
3ac841501feb2a5462250adf58dbb12a319f200ebe06966354584afd6438bf13
docker: Error response from daemon: driver failed programming external connectivity on endpoint mysql (6c5389e33e82a2c74c7ec43409528f6fec2941e8b3290f893300df1fc6ecc0fb): Error starting userland proxy: Bind for 0.0.0.0:3306 failed: port is already allocated.
➜  Seckill lsof -i tcp:3306
➜  Seckill 
```
####没占用也不行,一般建议换端口取别名