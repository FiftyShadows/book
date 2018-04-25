执行顺序

```sql
select * 
from 表名
where ...
group by ....having...
order by...
limit start ,count
```



#####1.进入数据库

`mysql -h 127.0.0.1 -u root -p`

#####2.创建数据库

` CREATE DATABASE 数据库名称 DEFAULT CHARSET utf8 COLLATE utf8_general_ci;`

#####3.打开数据库

`  USE db_name;`

#####4.显示库中的所有表
`show tables;`

#####5.用户的相关操作
```
创建用户
    create user '用户名'@'IP地址' identified by '密码';
删除用户
    drop user '用户名'@'IP地址';
修改用户
    rename user '用户名'@'IP地址'; to '新用户名'@'IP地址';;
修改密码
    set password for '用户名'@'IP地址' = Password('新密码')
所有授权
    grant all privileges on db1.tb1 TO '用户名'@'IP'
这条语句授予user_name用户针对database_name数据库的所有表的读权限。   
    GRANT SELECT ON database_name.* TO user_name;
```

#####6.创建表(对于自增列，必须是索引（含主键))
```sql
creat table color(
    nid int not null primary key  auto_increment,
    name char(16) not null
            )

create table fruit(
    nid int not null primary key,
    smt char(32) null ,
    color_id int not null,
    constraint fk_cc foreign key (color_id) references color(nid)
            )
```

#####7.删除和清空表
```
删除
    drop table 表名
清空
delete from 表名
truncate table 表名
```

#####8.修改
```
添加列：alter table 表名 add 列名 类型
删除列：alter table 表名 drop column 列名
修改列：
        alter table 表名 modify column 列名 新类型;  
        alter table 表名 change 原列名 新列名 类型; 
  
添加主键：
        alter table 表名 add primary key(列名);
删除主键：
        alter table 表名 drop primary key;
        alter table 表名  modify  列名 int, drop primary key;
  
添加外键：
    alter table 从表 add constraint 外键名称（形如：FK_从表_主表）             foreign key 从表(外键字段) references 主表(主键字段);
删除外键：
alter table 表名 drop foreign key 外键名称
  
修改默认值：ALTER TABLE testalter_tbl ALTER i SET DEFAULT 1000;
删除默认值：ALTER TABLE testalter_tbl ALTER i DROP DEFAULT;
```
#####9. 备份和还原

```sql

mysqldump -u username -p dbname table1 table2 > BackupName.sql

dbname参数表示数据库的名称；

table1和table2参数表示需要备份的表的名称，为空则表示整个数据库备份；
BackupName.sql参数表设计备份文件的名称，文件名前面可以加上一个绝对路径

例如:使用root用户备份test数据库下的person表
mysqldump -u root -p test person>D:\backup.sql
数据还原
mysql -u root -p [dbname] < backup.sql
例如:
mysql -u root -p < C:\backup.sql
```