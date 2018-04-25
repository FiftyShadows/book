![](/assets/WX20180424-100436@2x.png)

```sql
mysql> create table students( id int primary key auto_increment, stu_name char(20) );
Query OK, 0 rows affected (0.02 sec)

mysql> create table subjects( id int primary key auto_increment, sub_name char(20) );
Query OK, 0 rows affected (0.02 sec)

mysql> create table scores( id int  primary key  auto_increment, score int(4), constraint fk_score_stu foreign key (stu_id) references students(id), constraint fk_score_sub foreign key (sub_id) references subjects(id), sub_id int, stu_id int );
Query OK, 0 rows affected (0.02 sec)
```
数据如下
```sql
mysql> select * from students;
+----+--------------+
| id | stu_name     |
+----+--------------+
|  1 | 一号选手     |
|  2 | 二号选手     |
|  3 | 三号选手     |
|  4 | 四号选手     |
+----+--------------+
4 rows in set (0.00 sec)

mysql> select * from subjects;
+----+----------+
| id | sub_name |
+----+----------+
|  1 | 语文     |
|  2 | 数学     |
|  3 | 英语     |
+----+----------+
3 rows in set (0.00 sec)

mysql> select * from scores;
+----+-------+--------+--------+
| id | score | sub_id | stu_id |
+----+-------+--------+--------+
|  1 |    80 |      1 |      1 |
|  2 |    90 |      2 |      1 |
|  3 |    88 |      3 |      1 |
|  4 |    60 |      1 |      2 |
|  5 |    70 |      2 |      3 |
+----+-------+--------+--------+
5 rows in set (0.00 sec)
```
###内连接 全部都有的部分
```sql
mysql> SELECT scores.score,subjects.sub_name,students.stu_name from scores
    -> INNER JOIN students on students.id = scores.stu_id
    -> INNER join subjects on subjects.id = scores.sub_id;
+-------+----------+--------------+
| score | sub_name | stu_name     |
+-------+----------+--------------+
|    80 | 语文     | 一号选手     |
|    90 | 数学     | 一号选手     |
|    88 | 英语     | 一号选手     |
|    60 | 语文     | 二号选手     |
|    70 | 数学     | 三号选手     |
+-------+----------+--------------+
5 rows in set (0.00 sec)
```

###左连接 左边为准
```sql
mysql> SELECT scores.score,students.stu_name from scores                                               -> 
left JOIN students on students.id = scores.stu_id;
+-------+--------------+
| score | stu_name     |
+-------+--------------+
|    80 | 一号选手     |
|    90 | 一号选手     |
|    88 | 一号选手     |
|    60 | 二号选手     |
|    70 | 三号选手     |
+-------+--------------+
5 rows in set (0.00 sec)
```
###右连接  右边为准,没有则 null 代替

```sql
mysql> SELECT scores.score,students.stu_name from scores 
right JOIN students on students.id = scores.stu_id;
+-------+--------------+
| score | stu_name     |
+-------+--------------+
|    80 | 一号选手     |
|    90 | 一号选手     |
|    88 | 一号选手     |
|    60 | 二号选手     |
|    70 | 三号选手     |
|  NULL | 四号选手     |
+-------+--------------+
6 rows in set (0.00 sec)
```