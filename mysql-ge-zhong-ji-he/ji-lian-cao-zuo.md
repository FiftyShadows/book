
####一般做逻辑删除,而不是做物理删除,以下方案都一般

restrict(默认):默认,抛异常
set null: 设置为空
no action:什么都不做
cascade(级联):主表删除,从表的也删除

```sql
on delete cascade
on update cascade
```

buildings 对应多个 rooms

```sql
mysql> CREATE TABLE buildings (
    ->     building_no INT PRIMARY KEY AUTO_INCREMENT,
    ->     building_name VARCHAR(255) NOT NULL,
    ->     address VARCHAR(255) NOT NULL
    -> );
mysql> INSERT INTO buildings(building_name,address)
    -> VALUES('海南大厦','海口市国兴大道1234号'),
    ->       ('万达水城','海口市大同路1200号');
mysql> CREATE TABLE rooms (
    ->     room_no INT PRIMARY KEY AUTO_INCREMENT,
    ->     room_name VARCHAR(255) NOT NULL,
    ->     building_no INT NOT NULL,
    ->     FOREIGN KEY (building_no)
    ->         REFERENCES buildings (building_no)
    ->         ON DELETE CASCADE
    ->          ON UPDATE CASCADE ;  
mysql> INSERT INTO rooms(room_name,building_no)
    -> VALUES('Amazon',1),
    ->       ('War Room',1),
    ->       ('Office of CEO',1),
    ->       ('Marketing',2),
    ->       ('Showroom',2);
    
mysql> update buildings set building_no =3 where building_no=2;
mysql> select *from rooms;
+---------+---------------+-------------+
| room_no | room_name     | building_no |
+---------+---------------+-------------+
|       1 | Amazon        |           1 |
|       2 | War Room      |           1 |
|       3 | Office of CEO |           1 |
|       4 | Marketing     |           3 |
|       5 | Showroom      |           3 |
+---------+---------------+-------------+

```
