####MULTI 、 EXEC 、 DISCARD 和 WATCH 这四个命令是 Redis 事务的基础。

####开启
MULTI 开启一个事务。当客户端发出了MULTI 命令时，客户端和服务端的连接就进入了一个事务上下文的状态。MULTI 执行之后， 客户端可以继续向服务器发送任意多条命令， 这些命令不会立即被执行， 而是被放到一个队列中， 当 EXEC 命令被调用时， 所有队列中的命令才会被执行。
l EXEC 顺序执行事务队列中的命令。
```shell
192.168.2.129:6379> multi
OK
192.168.2.129:6379> set name "zhangsan"
QUEUED
192.168.2.129:6379> set age 20
QUEUED
192.168.2.129:6379> exec
1) OK
2) OK
192.168.2.129:6379> keys *
1) "age"
2) "name"
192.168.2.129:6379>
```
####取消
DISCARD 取消事务。当执行 DISCARD 命令时， 事务会被放弃， 事务队列会被清空， 并且客户端会从事务状态中退出。

```
192.168.2.129:6379> multi
OK
192.168.2.129:6379> set name2 "lisi"
QUEUED
192.168.2.129:6379> set age 22
QUEUED
192.168.2.129:6379> discard
OK
192.168.2.129:6379> exec
(error) ERR EXEC without MULTI
```
####乐观锁
l WATCH 对key值进行锁操作。 在 WATCH 执行之后， EXEC 执行之前， 有其他客户端修改了 key 的值， 那么当前客户端的事务就会失败。如下：

Client1开启watch name并在事务中修改name，但是没有执行exec
```
192.168.2.129:6379> get name
"huangliu"
192.168.2.129:6379> watch name
OK
192.168.2.129:6379> multi
OK
192.168.2.129:6379> set name lisi
QUEUED
```
Client2 修改name
```
192.168.2.129:6379> get name
"huangliu"
192.168.2.129:6379> set name "wangwu"
OK
192.168.2.129:6379> get name
"wangwu"
192.168.2.129:6379>
```
Client1执行exec
```
192.168.2.129:6379> exec
(nil)
192.168.2.129:6379>
```
由于被watch的name已经被Client2 修改，所以Client1的事务执行失败，程序需要做的， 就是不断重试这个操作， 直到没有发生碰撞（Crash）为止。对key进行加锁监视的机制类似Java多线程中的锁（synchronized中的监视器对象），被称作乐观锁。
