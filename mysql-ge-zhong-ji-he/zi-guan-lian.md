适用于结构很像表,比如评论,分类

![](/assets/QQ20180426-100722@2x.png)

创建
```sql
create table selfjoin(
id int primary key auto_increment,
name char(20),
pid int,
foreign key(pid) references selfjoin(id),
)
```

一共多少省
`select count(*) from selfjoin where pid = null;`

查询省名为浙江的所有市
```sql
select city.* from selfjoin as city
inner join selfjoin as province on city.pid = provic.id
where provice.name = "浙江"
```

查询宁波下的所有区

```sql
select dis.* from selfjoin as dis
inner join selfjoin as city on dis.pid = city.id
where city.name = "宁波"
```

