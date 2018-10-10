####花钱买集群是最理智的决定了

1. #####创建集群文件夹
在/usr/local/下新建redis-cluster目录并在redis-cluster下新建7031~7036共6个文件夹，这6个文件夹代表创建redis集群的6个节点。如下
```
[root@localhost local]# mkdir -p /usr/local/redis-cluster
[root@localhost redis-cluster]# mkdir 7031 7032 7033 7034 7035 7036
```
2. #####拷贝修改配置文件
将已有的/usr/local/redis/etc/下的redis.conf拷贝到新创建的7031目录中
```
[root@localhost etc]# cp redis.conf /usr/local/redis-cluster/7031
[root@localhost 7031]# vi redis.conf
```

修改项如下：

  (1）绑定端口，port 7031
（2）绑定IP，bind 192.168.2.128
（3）指定数据存放路径，dir /usr/local/redis-cluster/7031
（4）启动集群模式，cluster-enabled yes
（5）指定集群节点配置文件，cluster-config-file nodes-7031.conf
（6）后台启动，daemonize yes
（7）指定集群节点超时时间，cluster-node-timeout 5000
（8）指定持久化方式，appendonly yes

703x最好与节点文件夹保持一致。
将7031的redis.conf改完后再拷贝到剩下的5个目录中，然后只要全局替换redis.conf中的7031为相应的节点即可。

#####由于Redis 集群客户端实现很少，redis集群的启动需要用到ruby实现的redis-trib.rb，所以我们需要先安装ruby。
```
[root@localhost redis-cluster]# yum install ruby
[root@localhost redis-cluster]# yum install rubygems
[root@localhost redis-cluster]# gem install redis
```
#####启动redis实例

分别启动6个redis实例。也可以用脚本循环启动，这样更方便省时
```
[root@localhost redis-cluster]#
for((i=1;i<=6;i++)); do /usr/local/redis/bin/redis-server /usr/local/redis-cluster/703$i/redis.conf; done
```
查看redis实例是否启动成功
```
[root@localhost redis-cluster]# netstat -tunpl | grep redis-server
#或者
[root@localhost redis-cluster]# ps -ef | grep redis-server
```
#####创建并启动集群

进入redis安装目录的bin目录下
```
[root@localhost ~]# cd /usr/local/redis/bin/
[root@localhost bin]#./redis-trib.rb create --replicas 1 192.168.2.128:7031 192.168.2.128:7032 192.168.2.128:7033 192.168.2.128:7034 192.168.2.128:7035 192.168.2.128:7036 
```

命令的意义如下：
给定 redis-trib.rb 程序的命令是 create，表示创建一个新的集群。选项 --replicas 1 表示为集群中的每个主节点创建一个从节点。之后跟着的其他参数则是实例的地址列表， 指定使用这些地址所指示的实例来创建新集群。
```
>>> Creating cluster
>>> Performing hash slots allocation on 6 nodes...
Using 3 masters:
192.168.2.128:7031
192.168.2.128:7032
192.168.2.128:7033
Adding replica 192.168.2.128:7034 to 192.168.2.128:7031
Adding replica 192.168.2.128:7035 to 192.168.2.128:7032
Adding replica 192.168.2.128:7036 to 192.168.2.128:7033
......
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.
```
“All 16384 slots covered.”表示集群中的 16384 个槽都有至少一个主节点在处理， 集群运作正常。从打印出来的信息也可以看出，7031，7032，7033是主节点，其它三个是从节点。

#####客户端连接集群
```
[root@localhost bin]# /usr/local/redis/bin/redis-cli -c -h 192.168.2.128 -p 7031
192.168.2.128:7031> info
# Server
redis_version:3.2.0
redis_git_sha1:00000000
redis_git_dirty:0
redis_build_id:f8fcffd133fe3364
redis_mode:cluster
os:Linux 2.6.32-504.el6.x86_64 x86_64
arch_bits:64
```
可以使用 cluster info命令查看集群信息，cluster nodes命令查看集群节点信息。

#####集群关闭

关闭集群需要逐个关闭
```
[root@localhost redis-cluster]#
for((i=1;i<=6;i++)); do /usr/local/redis/bin/redis-cli -c -h 192.168.2.128 -p 703$i shutdown; done
```

如果重新启动集群报以下错误
```
[ERR] Node 192.168.2.128:7031 is not empty. Either the node already knows other nodes (check with CLUSTER NODES) or contains some key in database 0.
```
需要清除杀掉redis实例，然后删除每个节点下的临时数据文件appendonly.aof，dump.rdb，nodes-703x.conf，然后再重新启动redis实例即可启动集群。

```
[root@localhost redis-cluster]#for((i=1;i<=6;i++)); do cd 703$i; rm -rf appendonly.aof; rm -rf dump.rdb; rm -rf nodes-703$i.conf; cd ..; done
```

#####集群测试

```
192.168.2.128:7031> set name "zhangsan"
-> Redirected to slot [5798] located at 192.168.2.128:7032
OK
192.168.2.128:7032> set age 20
-> Redirected to slot [741] located at 192.168.2.128:7031
OK
192.168.2.128:7031> set sex "man"
OK
192.168.2.128:7031>
```

如果在连接客户端的时候不加-c选项set key时则会报MOVED 的错误：
```[root@localhost bin]# /usr/local/redis/bin/redis-cli -h 192.168.2.128 -p 7031
192.168.2.128:7031> set name "zhangsan"
(error) MOVED 5798 192.168.2.128:7032
192.168.2.128:7031> set age 20
OK
192.168.2.128:7031> set sex "man"
OK
192.168.2.128:7031>
```

取值

```
192.168.2.128:7031> get name
-> Redirected to slot [5798] located at 192.168.2.128:7032
"zhangsan"
192.168.2.128:7032> get age
-> Redirected to slot [741] located at 192.168.2.128:7031
"20"
192.168.2.128:7031> get sex
"man"
192.168.2.128:7031>
```

可以看到，客户端连接加-c选项的时候，存储和提取key的时候不断在7031和7032之间跳转，这个称为客户端重定向。之所以发生客户端重定向，是因为Redis Cluster中的每个Master节点都会负责一部分的槽(slot)，存取的时候都会进行键值空间计算定位key映射在哪个槽(slot)上，如果映射的槽(slot)正好是当前Master节点负责则直接存取，否则就跳转到其他Master节点负的槽(slot)中存取，这个过程对客户端是透明的。继续看下文的集群分区原理。

####Redis集群分区原理

从上面集群的简单操作中，我们已经知道redis存取key的时候，都要定位相应的槽(slot)。
Redis 集群键分布算法使用数据分片（sharding）而非一致性哈希（consistency hashing）来实现： 一个 Redis 集群包含 16384 个哈希槽（hash slot）， 它们的编号为0、1、2、3……16382、16383，这个槽是一个逻辑意义上的槽，实际上并不存在。redis中的每个key都属于这 16384 个哈希槽的其中一个，存取key时都要进行key->slot的映射计算。