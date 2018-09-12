####原子,一致,隔离,持久

```sql
mysql> select * from class;
+----+---------+
| id | caption |
+----+---------+
|  1 | 一班    |
|  2 | 二班    |
|  3 | 三班    |
+----+---------+
3 rows in set (0.01 sec)

mysql> begin;
Query OK, 0 rows affected (0.00 sec)

mysql> insert into class(caption) value("四班");
Query OK, 1 row affected (0.00 sec)

mysql> commit;
Query OK, 0 rows affected (0.00 sec)

mysql> select * from class;
+----+---------+
| id | caption |
+----+---------+
|  1 | 一班    |
|  2 | 二班    |
|  3 | 三班    |
|  4 | 四班    |
+----+---------+
4 rows in set (0.00 sec)
```
commit 换成rollback就会回滚,没有记录


####隔离级别

#####查看
`SELECT @@tx_isolation`
#####级别
1）read uncommitted : 读取尚未提交的数据 ：哪个问题都不能解决
2）read committed：读取已经提交的数据 ：可以解决脏读 ---- oracle默认的
3）repeatable read：重读读取：可以解决脏读 和 不可重复读 ---mysql默认的
4）serializable：串行化：可以解决 脏读 不可重复读 和 虚读---相当于锁表
#####设置
`set session transaction isolation level 设置事务隔离级别`

####实例

现在的表和数据
```sql
mysql> select * from accounts;
+----+---------+
| id | account |
+----+---------+
|  1 |     111 |
|  2 |     222 |
+----+---------+
```
打开两个控制台分别进行登录来模拟两个用户（暂且成为用户A和用户B吧），并设置当前MySQL会话的事务隔离级别。
#####A
```sql
mysql> set session transaction isolation level read uncommitted;
Query OK, 0 rows affected (0.00 sec)

mysql> start transaction;
Query OK, 0 rows affected (0.00 sec)
```
#####B
```sql
mysql> set session transaction isolation level read uncommitted;
Query OK, 0 rows affected (0.00 sec)

mysql> start transaction;
Query OK, 0 rows affected (0.00 sec)
mysql> update accounts set account=account+222 where id = 1;
```
A再查
```sql

mysql> select * from accounts;
+----+---------+
| id | account |
+----+---------+
|  1 |     333 |
|  2 |     222 |
+----+---------+
```
#####结论
即便是事务没有commit，但是我们仍然能读到未提交的数据,那就是我们在一个事务中可以随随便便读取到其他事务未提交的数据，这还是比较麻烦的，我们叫脏读。
实际上我们的数据改变了吗？
答案是否定的，因为只有事务commit后才会更新到数据库。

####在read committed级别中,只有 B commit;A 才能读取到

#####结论
当前会话只能读取到其他事务提交的数据，未提交的数据读不到。
在会话B同一个事务中，读取到两次不同的结果。这就造成了不可重复读，就是两次读取的结果不同。这种现象叫不可重复读。

#### repeatable read
A 插入一天数据, B 不能插入同 id 的

#####结论
当前会话可以重复读，就是每次读取的结果集都相同，而不管其他事务有没有提交。
要一个事务中读取的数据一致（可重复读）。我数据已经发生改变，但是我还是要保持一致。但是，出现了用户B面对的问题，这种现象叫幻读

#### serializable

A用户插入数据, B 用户等待,除非等待时间内 A提交,B 读出来,否则超时,等待时间可以设置

#####结论
当我们将当前会话的隔离级别设置为serializable的时候，其他会话对该表的写操作将被挂起。可以看到，这是隔离级别中最严格的，但是这样做势必对性能造成影响。