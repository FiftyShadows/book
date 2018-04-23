####约束是一种限制，它通过对表的行或列的数据做出限制，来确保表的数据的完整性、唯一性

1. 主键(PRIMARY KEY)是用于约束表中的一行，作为这一行的标识符，在一张表中通过主键就能准确定位到一行，因此主键十分重要。主键要求这一行的数据不能有重复且不能为空。

 还有一种特殊的主键——复合主键。主键不仅可以是表中的一列，也可以由表中的两列或多列来共同标识
 
2.  默认值约束(DEFAULT)规定，当有DEFAULT约束的列，插入数据为空时该怎么办。
DEFAULT约束只会在使用INSERT语句（上一实验介绍过）时体现出来，INSERT语句中，如果被DEFAULT约束的位置没有值，那么这个位置将会被DEFAULT的值填充

3. 唯一约束(UNIQUE)比较简单，它规定一张表中指定的一列的值必须不能有重复值，即这一列每个值都是唯一的。

当INSERT语句新插入的数据和已有数据重复的时候，如果有UNIQUE约束，则INSERT失败.

4. 外键(FOREIGN KEY)既能确保数据完整性，也能表现表之间的关系。

一个表可以有多个外键，每个外键必须REFERENCES(参考)另一个表的主键，被外键约束的列，取值必须在它参考的列中有对应值。

在INSERT时，如果被外键约束的值没有在参考列中有对应，比如以下命令，参考列(department表的dpt_name)中没有dpt3，则INSERT失败

5. 非空约束(NOT NULL),听名字就能理解，被非空约束的列，在插入值时必须非空。

在MySQL中违反非空约束，不会报错，只会有警告.

```sql
CREATE DATABASE mysql_shiyan;
  
 use mysql_text;
  
 CREATE TABLE department
 (
   dpt_name   CHAR(20) NOT NULL,
   people_num INT(10) DEFAULT '10',
   CONSTRAINT dpt_pk PRIMARY KEY (dpt_name)
  );
 
 CREATE TABLE employee
 (
   id      INT(10) PRIMARY KEY,
   name    CHAR(20),
   age     INT(10),
   salary  INT(10) NOT NULL,
   phone   INT(12) NOT NULL,
   in_dpt  CHAR(20) NOT NULL,
  UNIQUE  (phone),
   CONSTRAINT emp_fk FOREIGN KEY (in_dpt) REFERENCES department(dpt_name)
  );
  
 CREATE TABLE project
 (
   proj_num   INT(10) NOT NULL,
   proj_name  CHAR(20) NOT NULL,
   start_date DATE NOT NULL,
   end_date   DATE DEFAULT '2015-04-01',
   of_dpt     CHAR(20) REFERENCES department(dpt_name),
   CONSTRAINT proj_pk PRIMARY KEY (proj_num,proj_name)
  );
 ```