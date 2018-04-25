已知每页显示 m 条数据,当前显示第 n 页

查询总数:p1
页数为 p1/m 或者p1/m + 1

求第 n 页数据
```sql
select * from students
where age > 10
limit (n-1)*m,m
```