
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
 
 ```sql
 create table teacher(
id int primary key auto_increment,
name varchar(20)
on delete cascade
on update cascade
);

insert into teacher(name) value('ff'),('ff1'),('ff2');


create table course (
id int primary key auto_increment,
name varchar(20),
teacher_id int unique,
foreign key(teacher_id) references teacher(id)
);
```

###多对多
一个学生上多个课程,一个课程多个学生学,再创一个中间表

```sql
create table student2course (
id int primary key auto_increment,
student_id int,
course_id int,
foreign key(student_id) references student(id)
on delete cascade
on update cascade,
foreign key(course_id) references course(id)
on delete cascade
on update cascade,
score int
);

insert into student2course (student_id,course_id,score) values(1,1,60),(1,2,59),(2,2,100);
```
Sid Sname 
001 小王 
002 小李 
003 小张 
004 小刘 

Tid Tname 
001 数学 
002 语文 
003 英语

Sid Tid 
001 001 
001 002 
001 003 
002 001 
002 002 
003 003 

1.查询同时选修了数学和语文（不包含英语）的学生姓名
答：select \* from table1 where sid in (select Sid from table3 where Tid=001 and Tid=002) group by sid
2 查询至少选修了语文和数学（可能包含英语）的学生姓名
答：select \* from table1 where sid in (select Sid from table3 where Tid=001 and Tid=002 or Tid=003) group by sid
3 查询没有选修语文和数学的学生姓名
答：select \* from table1 where sid in(select Sid from table3 where Tid=003) group by sid

