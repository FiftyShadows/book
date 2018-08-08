######修改redis.conf可以配置密码
```
[root@localhost ~]# vi /usr/local/redis/etc/redis.conf

#######################SECURITY ##############################
......
# Warning: since Redis is pretty fast an outside user can try up to
# 150k passwords per second against a good box. This means that you should
# use a very strong password otherwise it will be very easy to break.
#
# requirepass foobared
requirepass redis129

# Command renaming.
```
如上，找到# requirepass foobared这一行，在下面添加“requirepass 密码”一行设置密码。设置好密码后，有两种方式授权客户端进行操作。
<br>

#####授权方式
1. 登录时使用-a参数指定客户端密码
```shell
[root@localhost ~]# /usr/local/redis/bin/redis-cli -h 192.168.2.129 -p 6379 -a redis129
192.168.2.129:6379> keys *
1) "myzset"
192.168.2.129:6379>
```
2. 登录客户端后使用auth命令进行授权
```shell
[root@localhost ~]# /usr/local/redis/bin/redis-cli -h 192.168.2.129 -p 6379
192.168.2.129:6379> keys *
(error) NOAUTH Authentication required.
192.168.2.129:6379> auth redis129
OK
192.168.2.129:6379> keys *
1) "myzset"
192.168.2.129:6379>
```
<br>
####主从复制的特点
（1）一个主服务器可以有多个从服务器。
（2）不仅主服务器可以有从服务器， 从服务器也可以有自己的从服务器。
（3）Redis 支持异步复制和部分复制（这两个特性从Redis 2.8开始），主从复制过程不会阻塞主服务器和从服务器。
（4）主从复制功能可以提升系统的伸缩性和功能，如让多个从服务器处理只读命令，使用复制功能来让主服务器免于频繁的执行持久化操作。
####过程
（1）从服务器都将向主服务器发送一个 SYNC 命令。
（2）主服务器接到 SYNC 命令后开启一个后台子进程并开始执行 BGSAVE，并在保存操作执行期间， 将所有新执行的写入命令都保存到一个缓冲区里面。
（3）当 BGSAVE 执行完毕后， 主服务器将执行保存操作所得的 .rdb 文件发送给从服务器， 从服务器接收这个 .rdb 文件， 并将文件中的数据载入到内存中。
（4）主服务器会以 Redis 命令协议的格式， 将写命令缓冲区中积累的所有内容都发送给从服务器。
![](/assets/v2-db43ccc87488fc4581fd1df5618c184b_hd.jpg)

<br>
####配置从服务器

 只要在从服务器的配置文件redis.conf中增加主服务器的IP地址和端口号就可以，如果主服务器设置了客户端密码，还需要在从服务器中配置主服务器的密码，如下
 
 ```
 ##########################REPLICATION ###############################

# Master-Slave replication. Use slaveof to make a Redis instance a copy of
# another Redis server. A few things to understand ASAP about Redis replication.
#
......
# slaveof <masterip> <masterport>
slaveof 192.168.2.129 6379
# If the master is password protected (using the "requirepass" configuration
# directive below) it is possible to tell the slave to authenticate before
# starting the replication synchronization process, otherwise the master will
# refuse the slave request.
#
# masterauth <master-password>
masterauth redis129
```
