####redis支持两种方式的持久化，一种是快照方式（snapshotting）,也称RDB方式；两一种是追加文件方式（append-only file），也称AOF方式。RDB方式是redis默认的持久化方式。
####rdb
RDB方式是将内存中的数据的快照以二进制的方式写入名字为 dump.rdb的文件中。我们对 Redis 进行设置， 让它根据设置周期性自动保存数据集。修改redis.conf文件，如下
dump.rdb文件默认生成在%REDIS_HOME%etc目录下（如/usr/local/redis/etc/），可以修改redis.conf文件中的dir指定dump.rdb的保存路径

```
######################### SNAPSHOTTING ################################
#
# Save the DB on disk:
......
# In the example below the behaviour will be to save:
# after 900 sec (15 min) if at least 1 key changed
# after 300 sec (5 min) if at least 10 keys changed
# after 60 sec if at least 10000 keys changed
#
# Note: you can disable saving completely by commenting out all "save" lines.
#
# It is also possible to remove all the previously configured save
# points by adding a save directive with a single empty string argument
# like in the following example:
#
# save ""
#900秒内如果有超过1个key被修改则发起保存快照
save 900 1
#300秒内如果有超过10个key被修改则发起保存快照
save 300 10
#60秒内如果有超过1000个key被修改则发起保存快照
save 60 10000
```
####aof
不适用,最好是做落库,也容易导致持久化文件过大,详见工作点滴记录


####比较
RDB与AOF同时开启 默认先加载AOF的配置文件


相同数据集，AOF文件要远大于RDB文件，恢复速度慢于RDB


AOF运行效率慢于RDB,但是同步策略效率好，不同步效率和RDB相同
