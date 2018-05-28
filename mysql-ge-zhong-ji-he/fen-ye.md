已知每页显示 m 条数据,当前显示第 n 页

查询总数:p1
页数为 p1/m 或者p1/m + 1

求第 n 页数据,遍历数据表
```sql
select * from students
where age > 10
limit (n-1)*m,m
```

第一种优化,走索引表,也不可取
```sql
select * from students where id >
(select id from students limit 10000,1)
limit 10
```

第二种优化,只有上一页下一页
```sql



```