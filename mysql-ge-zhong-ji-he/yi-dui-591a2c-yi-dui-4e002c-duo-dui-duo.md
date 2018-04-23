###一对多

#####一个班级对应多个学生
班级表
```sql
 create table class(
 id int primary key auto_increment ,
 caption char(20) 
 ); 
 ```
 班级数据
 ```sql
 insert into class (caption) values ("一班"),("二班"),("三班");
 ```
 学生表
 ```sql
 create table student( 
 id int primary key auto_increment, 
 name varchar(20), 
 sex enum("M","F") default "F", 
 class_id int, 
 foreign key(class_id) references class(id) 
 );
 ```
 学生数据
 ```sql
 insert into student(name,class_id) values ('钢弹',1),('铁锤',1),('山炮',2);
 ```
 
 ###一对一,就是把按个外键设置成唯一即可
 
 一个课程对应一个老师,一个老师只教一门课
 
 