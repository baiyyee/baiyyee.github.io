# mysql


### 安装
```
sudo apt-get install mysql-server

sudo service mysql start
```
>NOTE:
>
> ERROR 2002 (HY000): Can't connect to local MySQL server through socket '/var/run/mysqld/mysqld.sock' (2)
> https://blog.csdn.net/weixin_39345384/article/details/80855359

<br>

### 登录 & 退出
```
-- 登录
mysql -uroot -p123456

-- 退出数据库服务器
mysql> exit;
Bye
```

<br>

### 数据库操作
```
-- 查询数据库服务器中所有的数据库
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+


-- 创建数据库 hhbdb
mysql> create database hhbdb;
Query OK, 1 row affected (0.03 sec)


-- 使用某个数据库
mysql> use hhbdb;
Database changed

```

<br>

### 数据类型
```
数值：
    tinyint     - 1个字节
    smallint    - 2个字节
    mediumint   - 3个字节
    int/integer - 4个字节
    bigint      - 8个字节
    float       - 4个字节
    double      - 8个字节
    decimal     - 

时间：
    date
    time
    year
    datetime
    timestamp

字符串：
    类型	    大小	                 用途
    CHAR	    0-255 bytes	            定长字符串
    VARCHAR	    0-65535 bytes	        变长字符串
    TINYBLOB	0-255 bytes	            不超过 255 个字符的二进制字符串
    TINYTEXT	0-255 bytes	            短文本字符串
    BLOB	    0-65 535 bytes	        二进制形式的长文本数据
    TEXT	    0-65 535 bytes	        长文本数据
    MEDIUMBLOB	0-16 777 215 bytes	    二进制形式的中等长度文本数据
    MEDIUMTEXT	0-16 777 215 bytes	    中等长度文本数据
    LONGBLOB	0-4 294 967 295 bytes	二进制形式的极大文本数据
    LONGTEXT	0-4 294 967 295 bytes	极大文本数据
```

<br>

### 数据表操作
```sql
-- 创建数据表
mysql> create table pet (name varchar(20), owner varchar(20), species varchar(20), sex char(1), birth date, death date);
Query OK, 0 rows affected (0.19 sec)


-- 查看某个数据库中的所有表
mysql> show tables;
+-----------------+
| Tables_in_hhbdb |
+-----------------+
| pet             |
+-----------------+


-- 查看数据表的表结构

-- 方式一(describe 和 desc 等效)

mysql> describe pet;
+---------+-------------+------+-----+---------+-------+
| Field   | Type        | Null | Key | Default | Extra |
+---------+-------------+------+-----+---------+-------+
| name    | varchar(20) | YES  |     | NULL    |       |
| owner   | varchar(20) | YES  |     | NULL    |       |
| species | varchar(20) | YES  |     | NULL    |       |
| sex     | char(1)     | YES  |     | NULL    |       |
| birth   | date        | YES  |     | NULL    |       |
| death   | date        | YES  |     | NULL    |       |
+---------+-------------+------+-----+---------+-------+


-- 方式二
mysql> show columns from pet;
+---------+-------------+------+-----+---------+-------+
| Field   | Type        | Null | Key | Default | Extra |
+---------+-------------+------+-----+---------+-------+
| name    | varchar(20) | YES  |     | NULL    |       |
| owner   | varchar(20) | YES  |     | NULL    |       |
| species | varchar(20) | YES  |     | NULL    |       |
| sex     | char(1)     | YES  |     | NULL    |       |
| birth   | date        | YES  |     | NULL    |       |
| death   | date        | YES  |     | NULL    |       |
+---------+-------------+------+-----+---------+-------+



-- 往数据表中插入单行数据记录
mysql> insert into pet values ("Puffball", "Diane", "hamster","f","2022-02-14", NULL);
Query OK, 1 row affected (0.06 sec)

-- 往数据表中插入多行数据记录
mysql> insert into pet values ("旺财", "周星驰", "狗", "公", "2022-02-15", NULL), ("哮天犬", "二郎神", "狗", "公", "2022-02-15", NULL);
Query OK, 2 rows affected (0.02 sec)
Records: 2  Duplicates: 0  Warnings: 0

-- 指定字段顺序插入
mysql> insert into pet (name, owner, species, sex, birth, death) values ("旺财", "周星驰", "狗", "公", "2022-02-15", NULL), ("哮天犬", "二郎神", "狗", "公", "2022-02-15", NULL);


-- 查看某个表中的所有数据记录
mysql> select * from pet;
+-----------+-----------+---------+------+------------+-------+
| name      | owner     | species | sex  | birth      | death |
+-----------+-----------+---------+------+------------+-------+
| Puffball  | Diane     | hamster | f    | 2022-02-14 | NULL  |
| 旺财      | 周星驰    | 狗      | 公   | 2022-02-15 | NULL  |
| 哮天犬    | 二郎神    | 狗      | 公   | 2022-02-15 | NULL  |
+-----------+-----------+---------+------+------------+-------+



-- 条件查询
mysql> select * from pet where owner = "Diane";
+----------+-------+---------+------+------------+-------+
| name     | owner | species | sex  | birth      | death |
+----------+-------+---------+------+------------+-------+
| Puffball | Diane | hamster | f    | 2022-02-14 | NULL  |
+----------+-------+---------+------+------------+-------+


-- 删除数据
mysql> delete from pet where owner = "Diane";
Query OK, 1 row affected (0.02 sec)


-- 清空表数据
truncate table pet;

注意：
    - 不能与where一起使用。
    - truncate删除数据后是不可以rollback的。
    - truncate删除数据后会重置Identity（标识列、自增字段），相当于自增列会被置为初始值，又重新从1开始记录，而不是接着原来的ID数
    - truncate删除数据后不写服务器log，整体删除速度快
    - truncate删除数据后不激活trigger(触发器)


-- 删除表
mysql> drop table myorder1;
Query OK, 0 rows affected (0.03 sec)


-- 更新数据
mysql> update pet set name = "旺财update" where name = "旺财";
Query OK, 1 row affected (0.02 sec)
```

<br>

### mysql 约束
```sql
-- 主键约束(primary key): 唯一确定一张表中的一条记录，主键约束字段不重复且不为空

mysql> create table user(id int primary key, name varchar(20));
Query OK, 0 rows affected (0.09 sec)

mysql> describe user;
+-------+-------------+------+-----+---------+-------+
| Field | Type        | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
| id    | int         | NO   | PRI | NULL    |       |
| name  | varchar(20) | YES  |     | NULL    |       |
+-------+-------------+------+-----+---------+-------+


mysql> insert into user (id, name) values (1, "张三"), (2, "李四"), (3, "王五");
Query OK, 3 rows affected (0.01 sec)

-- 主键不允许重复
mysql> insert into user (id, name) values (1, "张三");
ERROR 1062 (23000): Duplicate entry '1' for key 'user.PRIMARY'


-- 主键不允许为空
mysql> insert into user (id, name) values (NULL, "张三");
ERROR 1048 (23000): Column 'id' cannot be null


-- 联合主键：多个主键组合起来不重复即可插入成功
mysql> create table student (id int, name varchar(20), primary key(id, name));
Query OK, 0 rows affected (0.09 sec)

mysql> describe student;
+-------+-------------+------+-----+---------+-------+
| Field | Type        | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
| id    | int         | NO   | PRI | NULL    |       |
| name  | varchar(20) | NO   | PRI | NULL    |       |
+-------+-------------+------+-----+---------+-------+

mysql> insert into student values (1, "张三");
Query OK, 1 row affected (0.02 sec)

mysql> insert into student values (1, "张三");
ERROR 1062 (23000): Duplicate entry '1-张三' for key 'student.PRIMARY'
mysql> insert into student values (2, "张三");
Query OK, 1 row affected (0.02 sec)


-- 修改表名
mysql> alter table student rename user2;
Query OK, 0 rows affected (0.07 sec)


-- 自增约束(auto_increment)

mysql> create table user3 (id int primary key auto_increment, name varchar(20));
Query OK, 0 rows affected (0.09 sec)

mysql> insert into user3 (name) values ("张三");
Query OK, 1 row affected (0.03 sec)

mysql> select * from user3;
+----+--------+
| id | name   |
+----+--------+
|  1 | 张三   |
+----+--------+
1 row in set (0.00 sec)


-- 给某个字段添加主键约束

mysql> create table user4 (id int, name varchar(20));
Query OK, 0 rows affected (0.07 sec)

mysql> describe user4;
+-------+-------------+------+-----+---------+-------+
| Field | Type        | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
| id    | int         | YES  |     | NULL    |       |
| name  | varchar(20) | YES  |     | NULL    |       |
+-------+-------------+------+-----+---------+-------+
2 rows in set (0.01 sec)

mysql> alter table user4 add primary key(id);
Query OK, 0 rows affected (0.15 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> describe user4;
+-------+-------------+------+-----+---------+-------+
| Field | Type        | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
| id    | int         | NO   | PRI | NULL    |       |
| name  | varchar(20) | YES  |     | NULL    |       |
+-------+-------------+------+-----+---------+-------+
2 rows in set (0.01 sec)


-- 删除主键约束(drop primary key)

mysql> alter table user4 drop primary key;
Query OK, 0 rows affected (0.14 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> describe user4;
+-------+-------------+------+-----+---------+-------+
| Field | Type        | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
| id    | int         | NO   |     | NULL    |       |
| name  | varchar(20) | YES  |     | NULL    |       |
+-------+-------------+------+-----+---------+-------+


-- 修改字段属性(modify)

mysql> alter table user4 modify id int primary key;
Query OK, 0 rows affected (0.11 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> describe user4;
+-------+-------------+------+-----+---------+-------+
| Field | Type        | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
| id    | int         | NO   | PRI | NULL    |       |
| name  | varchar(20) | YES  |     | NULL    |       |
+-------+-------------+------+-----+---------+-------+
2 rows in set (0.00 sec)


-- 唯一约束(unique)：约束修饰的字段不允许重复

-- 方式一：
mysql> create table user5 (id int, name varchar(20));
Query OK, 0 rows affected (0.07 sec)

mysql> alter table user5 add unique(name);
Query OK, 0 rows affected (0.07 sec)
Records: 0  Duplicates: 0  Warnings: 0


mysql> describe user5;
+-------+-------------+------+-----+---------+-------+
| Field | Type        | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
| id    | int         | YES  |     | NULL    |       |
| name  | varchar(20) | YES  | UNI | NULL    |       |
+-------+-------------+------+-----+---------+-------+
2 rows in set (0.00 sec)

mysql> insert into user5 values (1, "张三");
Query OK, 1 row affected (0.01 sec)

mysql> insert into user5 values (2, "张三");
ERROR 1062 (23000): Duplicate entry '张三' for key 'user5.name'

-- 方式二：
mysql> create table user6 (id int, name varchar(20), unique(name));
Query OK, 0 rows affected (0.08 sec)

mysql> describe user6;
+-------+-------------+------+-----+---------+-------+
| Field | Type        | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
| id    | int         | YES  |     | NULL    |       |
| name  | varchar(20) | YES  | UNI | NULL    |       |
+-------+-------------+------+-----+---------+-------+
2 rows in set (0.01 sec)

-- 方式三：
mysql> create table user7 (id int, name varchar(20) unique);
Query OK, 0 rows affected (0.08 sec)

mysql> desc user7;
+-------+-------------+------+-----+---------+-------+
| Field | Type        | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
| id    | int         | YES  |     | NULL    |       |
| name  | varchar(20) | YES  | UNI | NULL    |       |
+-------+-------------+------+-----+---------+-------+


-- 联合唯一约束：多个被修饰的字段组合起来不重复即可插入成功
mysql> create table user8 (id int, name varchar(20), unique(id, name));
Query OK, 0 rows affected (0.07 sec)

mysql> insert into user8 values (1, "张三");
Query OK, 1 row affected (0.01 sec)

mysql> insert into user8 values (1, "张三");
ERROR 1062 (23000): Duplicate entry '1-张三' for key 'user8.id'
mysql> insert into user8 values (2, "张三");
Query OK, 1 row affected (0.02 sec)

mysql> select * from user8;
+------+--------+
| id   | name   |
+------+--------+
|    1 | 张三   |
|    2 | 张三   |
+------+--------+


-- 删除唯一约束

mysql> desc user7;
+-------+-------------+------+-----+---------+-------+
| Field | Type        | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
| id    | int         | YES  |     | NULL    |       |
| name  | varchar(20) | YES  | UNI | NULL    |       |
+-------+-------------+------+-----+---------+-------+
2 rows in set (0.00 sec)

mysql> alter table user7 drop index name;
Query OK, 0 rows affected (0.06 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> desc user7;
+-------+-------------+------+-----+---------+-------+
| Field | Type        | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
| id    | int         | YES  |     | NULL    |       |
| name  | varchar(20) | YES  |     | NULL    |       |
+-------+-------------+------+-----+---------+-------+



-- 修改字段的唯一属性

mysql> alter table user7 modify name varchar(20) unique;
Query OK, 0 rows affected (0.05 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> desc user7;
+-------+-------------+------+-----+---------+-------+
| Field | Type        | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
| id    | int         | YES  |     | NULL    |       |
| name  | varchar(20) | YES  | UNI | NULL    |       |
+-------+-------------+------+-----+---------+-------+


-- 非空约束(not null)

mysql> create table user9 (id int, name varchar(20) not null);
Query OK, 0 rows affected (0.08 sec)

mysql> desc user9;
+-------+-------------+------+-----+---------+-------+
| Field | Type        | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
| id    | int         | YES  |     | NULL    |       |
| name  | varchar(20) | NO   |     | NULL    |       |
+-------+-------------+------+-----+---------+-------+

mysql> insert into user9 values (1);


-- 修改字段属性 - 移除非空约束

mysql> alter table user9 modify name varchar(20);
Query OK, 0 rows affected (0.13 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> desc user9;
+-------+-------------+------+-----+---------+-------+
| Field | Type        | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
| id    | int         | YES  |     | NULL    |       |
| name  | varchar(20) | YES  |     | NULL    |       |
+-------+-------------+------+-----+---------+-------+


-- 修改字段属性 - 新增非空约束
mysql> alter table user9 modify name varchar(20) not null;
Query OK, 0 rows affected (0.10 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> desc user9;
+-------+-------------+------+-----+---------+-------+
| Field | Type        | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
| id    | int         | YES  |     | NULL    |       |
| name  | varchar(20) | NO   |     | NULL    |       |
+-------+-------------+------+-----+---------+-------+


-- 默认约束(default)：插入数据时没传值则使用默认值

mysql> create table user10 (id int, name varchar(20), age int default 18);
Query OK, 0 rows affected (0.07 sec)

mysql> desc user10;
+-------+-------------+------+-----+---------+-------+
| Field | Type        | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
| id    | int         | YES  |     | NULL    |       |
| name  | varchar(20) | YES  |     | NULL    |       |
| age   | int         | YES  |     | 18      |       |
+-------+-------------+------+-----+---------+-------+


mysql> insert into user10 (id,name) values (1, "张三");
Query OK, 1 row affected (0.02 sec)

mysql> select * from user10;
+------+--------+------+
| id   | name   | age  |
+------+--------+------+
|    1 | 张三   |   18 |
+------+--------+------+


-- 外键约束(foreign key)：涉及多个表(父表-子表，主表-副表)

-- 班级表
mysql> create table class(id int primary key, name varchar(20));
Query OK, 0 rows affected (0.07 sec)

insert into class values (1, "三年级一班"), (2, "三年级二班"), (3, "三年级三班");

mysql> select * from class;
+----+-----------------+
| id | name            |
+----+-----------------+
|  1 | 三年级一班      |
|  2 | 三年级二班      |
|  3 | 三年级三班      |
+----+-----------------+

-- 学生表
mysql> create table student(id int primary key, name varchar(20), class_id int, foreign key(class_id) references class(id));
Query OK, 0 rows affected (0.11 sec)


mysql> insert into student values (1, "张三", 1);
Query OK, 1 row affected (0.01 sec)


-- 主表中没有的数据，在副表中不可用使用
mysql> insert into student values (1, "张三", 9);
ERROR 1062 (23000): Duplicate entry '1' for key 'student.PRIMARY'


-- 主表中的记录被副表引用，不可用被删除
mysql> delete from class where id = 1;
ERROR 1451 (23000): Cannot delete or update a parent row: a foreign key constraint fails (`hhbdb`.`student`, CONSTRAINT `student_ibfk_1` FOREIGN KEY (`class_id`) REFERENCES `class` (`id`))
```

<br>

### 数据库范式
```sql
-- 第一范式：数据表中的所有字段都是不可分割的原子值

mysql> create table student2 (id int primary key, name varchar(20), address varchar(30));
Query OK, 0 rows affected (0.08 sec)

mysql> insert into student2 values (1, "张三", "中国上海市闵行区浦江镇三达路112号"),(2, "李四", "中国上海市闵行区浦江镇三达路113号"), (3, "王五", "中国上海市闵行区浦江镇三达路114号");
Query OK, 3 rows affected (0.01 sec)
Records: 3  Duplicates: 0  Warnings: 0

mysql> select * from student2;
+----+--------+--------------------------------------------------+
| id | name   | address                                          |
+----+--------+--------------------------------------------------+
|  1 | 张三   | 中国上海市闵行区浦江镇三达路112号                |
|  2 | 李四   | 中国上海市闵行区浦江镇三达路113号                |
|  3 | 王五   | 中国上海市闵行区浦江镇三达路114号                |
+----+--------+--------------------------------------------------+


以上表设计中，address 字段还可以继续拆分，就不满足第一范式

改进如下：address => country, province, city, details



mysql> create table student3 (id int primary key, name varchar(20), country varchar(20), province varchar(20), city varchar(20), details varchar(30));

insert into student3 values (1, "张三", "中国", "上海市", "上海市", "闵行区浦江镇三达路112号");


mysql> insert into student3 values (1, "张三", "中国", "上海市", "上海市", "闵行区浦江镇三达路112号");
Query OK, 1 row affected (0.01 sec)

mysql> select * from student3;
+----+--------+---------+-----------+-----------+-----------------------------------+
| id | name   | country | province  | city      | details                           |
+----+--------+---------+-----------+-----------+-----------------------------------+
|  1 | 张三   | 中国    | 上海市    | 上海市    | 闵行区浦江镇三达路112号           |
+----+--------+---------+-----------+-----------+-----------------------------------+


-- NOTE：范式，设计的越详细，对于数据分析维度可能更好，但过于设计会导致数据库设计的复杂度增加，有可能设计的字段并不会产生实际用途，需要结合实际场景来设计，避免过度设计


-- 第二范式：在满足第一范式的前提下，第二范式要求，除主键外的每一列都必须完全依赖于主键，如果出现不完全依赖，只可能发生在联合主键的情况下

-- 订单表

create table myorder1 (product_id int, customer_id int, product_name varchar(20), customer_name varchar(20), primary key(product_id, customer_id));


-- 问题：以上表设计，product_name 只与 product_id 有关, customer_name 只与 customer_id 有关，并不满足除主键外的所有列都依赖于主键的第二范式要求

-- 改进如下：


create table product (id int primary key, name varchar(20));
create table customer (id int primary key, name varchar(20));
create table myorder2 (id int primary key, product_id int, customer_id int);


-- 拆成3个表后，就满足了第二范式


-- 第三范式：在满足第二范式前提下，除开主键列外的其他列之间不能有传递依赖关系

-- 订单表

create table myorder3 (id int primary key, product_id int, customer_id int, customer_phone varchar(15));


-- 问题：以上表设计中，customer_phone 依赖于 customer_id 字段，customer_id 字段又依赖于 id, 字段之间存在冗余关系，不满足第三范式

-- 改进：把 customer_phone 放到 customer 表中

create table myorder3 (id int primary key, product_id int, customer_id int);
create table customer (id int primary key, name varchar(20), customer_phone varchar(15));
```

<br>

### mysql 查询
```sql
-- 学生表
create table student4 (sno varchar(20) primary key, sname varchar(20) not null, ssex varchar(10) not null, sbirthday datetime, class varchar(20));
insert into student4 values ("001", "张三", "男", "1990-01-01", "一班"), ("002", "李四", "女", "1990-02-02", "二班"), ("003", "王五", "男", "1990-03-03", "三班"), ("004", "赵六", "女", "1990-04-04", "四班");


-- 教师表
create table teacher (tno varchar(20) primary key, tname varchar(20) not null, tsex varchar(10) not null, tbirthday datetime, prof varchar(20), depart varchar(20));
insert into teacher values ("001", "张老师", "男", "1990-01-01", "数据库", "计算机学院"), ("002", "李老师", "女", "1990-02-02", "数据库", "计算机学院"), ("003", "王老师", "男", "1990-03-03", "数据库", "计算机学院"), ("004", "赵老师", "女", "1990-04-04", "数据库", "计算机学院");


-- 课程表
create table course(cno varchar(20) primary key, cname varchar(20) not null, tno varchar(20), foreign key(tno) references teacher(tno));
insert into course values ("001", "数据库原理", "001"), ("002", "数据库设计", "002"), ("003", "数据库系统", "003"), ("004", "数据库系统实践", "004");


-- 成绩表
create table score (sno varchar(20), cno varchar(20) not null, degree decimal, foreign key (sno) references student4(sno), foreign key (cno) references course(cno));
insert into score (sno, cno, degree) values ("001", "001", 95), ("002", "002", 90), ("003", "003", 85), ("004", "004", 80), ("001", "004", 70);


-- 等级表
create table grade(low int(3), upper int(3), grade char(1));
insert into grade values (0, 59, 'D'), (60, 79, 'C'), (80, 89, 'B'), (90, 100, 'A');

-- 删除外键约束
mysql> show create table score;
+-------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Table | Create Table                                                                                                                                                                                                                            |
+-------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| score | CREATE TABLE `score` (
  `sno` varchar(20) NOT NULL,
  `cno` varchar(20) NOT NULL,
  `degree` decimal(10,0) DEFAULT NULL,
  PRIMARY KEY (`sno`),
  KEY `cno` (`cno`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci |
+-------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)


mysql> alter table score drop foreign key score_ibfk_1;
Query OK, 0 rows affected (0.05 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> alter table score drop foreign key score_ibfk_2;
Query OK, 0 rows affected (0.04 sec)
Records: 0  Duplicates: 0  Warnings: 0


-- 查询 学生表 所有记录
mysql> select * from student4;
+-----+--------+------+---------------------+--------+
| sno | sname  | ssex | sbirthday           | class  |
+-----+--------+------+---------------------+--------+
| 001 | 张三   | 男   | 1990-01-01 00:00:00 | 一班   |
| 002 | 李四   | 女   | 1990-02-02 00:00:00 | 二班   |
| 003 | 王五   | 男   | 1990-03-03 00:00:00 | 三班   |
| 004 | 赵六   | 女   | 1990-04-04 00:00:00 | 四班   |
+-----+--------+------+---------------------+--------+


-- 查询 学生表 的 sname, ssex, class 列
mysql> select sname,ssex,class from student4;
+--------+------+--------+
| sname  | ssex | class  |
+--------+------+--------+
| 张三   | 男   | 一班   |
| 李四   | 女   | 二班   |
| 王五   | 男   | 三班   |
| 赵六   | 女   | 四班   |
+--------+------+--------+


-- 查询 学生表 中 class (去重)
mysql> select distinct class from student4;
+--------+
| class  |
+--------+
| 一班   |
| 二班   |
| 三班   |
| 四班   |
+--------+


-- 查询 score 表中成绩在 80 - 90的记录
mysql> select degree from score where degree between 80 and 90;
+--------+
| degree |
+--------+
|     90 |
|     85 |
|     80 |
+--------+

-- 方法二：运算符比较
mysql> select degree from score where degree >= 80 and degree <= 90;
+--------+
| degree |
+--------+
|     90 |
|     85 |
|     80 |
+--------+



-- 查询 score 表中成绩为 90 和 95 的数据记录
mysql> select degree from score where degree in (90, 95);
+--------+
| degree |
+--------+
|     95 |
|     90 |
+--------+



-- 查询 学生表 中性别为 男 或 班级为 二班 的数据记录
mysql> select * from student4 where ssex="男" or class="二班";
+-----+--------+------+---------------------+--------+
| sno | sname  | ssex | sbirthday           | class  |
+-----+--------+------+---------------------+--------+
| 001 | 张三   | 男   | 1990-01-01 00:00:00 | 一班   |
| 002 | 李四   | 女   | 1990-02-02 00:00:00 | 二班   |
| 003 | 王五   | 男   | 1990-03-03 00:00:00 | 三班   |
+-----+--------+------+---------------------+--------+


-- 查询 成绩表 按分数升序排序（默认升序，可省略 asc）
mysql> select * from score order by degree asc;
+-----+-----+--------+
| sno | cno | degree |
+-----+-----+--------+
| 004 | 004 |     80 |
| 003 | 003 |     85 |
| 002 | 002 |     90 |
| 001 | 001 |     95 |
+-----+-----+--------+


-- 查询 成绩表 按分数降序排序

mysql> select * from score order by degree desc;
+-----+-----+--------+
| sno | cno | degree |
+-----+-----+--------+
| 001 | 001 |     95 |
| 002 | 002 |     90 |
| 003 | 003 |     85 |
| 004 | 004 |     80 |
+-----+-----+--------+


-- 以 sno 升序 且 degree 降序 查询 成绩表
mysql> select * from score order by sno asc, degree desc;
+-----+-----+--------+
| sno | cno | degree |
+-----+-----+--------+
| 001 | 001 |     95 |
| 002 | 002 |     90 |
| 003 | 003 |     85 |
| 004 | 004 |     80 |
+-----+-----+--------+


-- 查询 一班 的学生人数
mysql> select count(*) from student4 where class="一班";
+----------+
| count(1) |
+----------+
|        1 |
+----------+

-- 有主键或联合主键的情况下，count(*)略比count(1)快一些。 没有主键的情况下count(1)比count(*)快一些。 



-- 查询 学生表 中分数最高的学生学号和课程号 => 子查询或排序

-- 子查询
mysql> select sno, cno from score where degree = (select max(degree) from score);
+-----+-----+
| sno | cno |
+-----+-----+
| 001 | 001 |
+-----+-----+

-- 排序(有bug: 如果最高分有多个会有问题，limit 未知)
mysql> select sno, cno from score order by degree desc limit 1;
+-----+-----+
| sno | cno |
+-----+-----+
| 001 | 001 |
+-----+-----+


-- 限制输出行数 limit

LIMIT后面，可以跟两个参数

第一个参数，OFFSET，可选参数，表示偏移量，如果不指定默认值为0, 如果，偏移量为0，则从查询结果的第一条记录开始，偏移量为1，则从查询结果中的第二条记录开始，以此类推(等同于编程语言里面的索引，从0开始)
第二个参数，记录数，表示返回查询结果的条数

-- 输出1行
mysql> select sno, cno from score order by degree desc limit 1;
+-----+-----+
| sno | cno |
+-----+-----+
| 001 | 001 |
+-----+-----+

-- 输出第1-3行
mysql> select sno, cno from score order by degree desc limit 1,3;
+-----+-----+
| sno | cno |
+-----+-----+
| 002 | 002 |
| 003 | 003 |
| 004 | 004 |
+-----+-----+


-- 查询每门课程的平均成绩
mysql> select cno,avg(degree) from score group by cno;
+-----+-------------+
| cno | avg(degree) |
+-----+-------------+
| 001 |     95.0000 |
| 002 |     90.0000 |
| 003 |     85.0000 |
| 004 |     80.0000 |
+-----+-------------+



-- 保留指定位数的小数

-- 四舍五入
mysql> select round(12.34567, 2);
+--------------------+
| round(12.34567, 2) |
+--------------------+
|              12.35 |
+--------------------+
1 row in set (0.01 sec)

-- 四舍五入并返回字符串
mysql> select format(12.34567, 2);
+---------------------+
| format(12.34567, 2) |
+---------------------+
| 12.35               |
+---------------------+
1 row in set (0.01 sec)

-- 非四舍五入强制截取
mysql> select truncate(12.34567, 2);
+-----------------------+
| truncate(12.34567, 2) |
+-----------------------+
|                 12.34 |
+-----------------------+


-- 查询 成绩表 中 至少有2名学生选修并以3开头额课程的平均分数
mysql> select cno,avg(degree),count(*) from score group by cno having count(cno) >=2 and cno like "3%";
+-----+-------------+----------+
| cno | avg(degree) | count(*) |
+-----+-------------+----------+
| 004 |     75.0000 |        2 |
+-----+-------------+----------+
1 row in set (0.05 sec)


-- NOTE: 遇到复杂查询操作时，需要分步分析


-- 查询分数大于70且小于90的sno列
mysql> select distinct(sno) from score where degree > 70 and degree < 90;
+-----+
| sno |
+-----+
| 001 |
| 002 |
| 003 |
| 004 |
+-----+
4 rows in set (0.01 sec)


-- NOTE: where degree > 70 and degree < 90 和 where degree between 70 and 90 是不同的查询，between ... and 是闭区间，而 > and < 是开区间


-- 查询所有学生的 sname, cno, degree （多表查询）

-- 方法1：
mysql> select sname,cno,degree from score inner join student4 on score.sno = student4.sno;
+--------+-----+--------+
| sname  | cno | degree |
+--------+-----+--------+
| 张三   | 001 |     95 |
| 张三   | 004 |     70 |
| 李四   | 002 |     90 |
| 王五   | 003 |     85 |
| 赵六   | 004 |     80 |
+--------+-----+--------+

-- 方法2：
mysql> select sname,cno,degree from score,student4 where score.sno = student4.sno;
+--------+-----+--------+
| sname  | cno | degree |
+--------+-----+--------+
| 张三   | 001 |     95 |
| 张三   | 004 |     70 |
| 李四   | 002 |     90 |
| 王五   | 003 |     85 |
| 赵六   | 004 |     80 |
+--------+-----+--------+


-- 查询所有学生的 sname, cname, degree

mysql> select sname,cname,degree from score inner join student4 inner join course on score.sno=student4.sno and score.cno=course.cno;
+--------+-----------------------+--------+
| sname  | cname                 | degree |
+--------+-----------------------+--------+
| 张三   | 数据库原理            |     95 |
| 张三   | 数据库系统实践        |     70 |
| 李四   | 数据库设计            |     90 |
| 王五   | 数据库系统            |     85 |
| 赵六   | 数据库系统实践        |     80 |
+--------+-----------------------+--------+


-- 查询 一班 学生每门课的平均分
mysql> select avg(degree),cname,class from score inner join student4 inner join course on score.sno=student4.sno and score.cno=course.cno where class="一班" group by cname;
+-------------+-----------------------+--------+
| avg(degree) | cname                 | class  |
+-------------+-----------------------+--------+
|     95.0000 | 数据库原理            | 一班   |
|     70.0000 | 数据库系统实践        | 一班   |
+-------------+-----------------------+--------+


-- 查询选修 004 课程的成绩高于 001号 同学 004 课程成绩的所有记录
mysql> select * from score where degree > (select degree from score where sno="001" and cno="004") and cno="004";
+-----+-----+--------+
| sno | cno | degree |
+-----+-----+--------+
| 004 | 004 |     80 |
+-----+-----+--------+


-- 查询成绩高于学号为 001 号，课程号为 004 的学生的成绩的所有记录
mysql> select * from score where degree > (select degree from score where sno="001" and cno="004");
+-----+-----+--------+
| sno | cno | degree |
+-----+-----+--------+
| 001 | 001 |     95 |
| 002 | 002 |     90 |
| 003 | 003 |     85 |
| 004 | 004 |     80 |
+-----+-----+--------+


-- 查询和学号为 001，002 号的同学同年出生的所有学生的 sno,sname,sbirthday 列
mysql> select sno,sname,sbirthday from student4 where year(sbirthday) in (select year(sbirthday) from student4 where sno in ("001", "002"));
+-----+--------+---------------------+
| sno | sname  | sbirthday           |
+-----+--------+---------------------+
| 001 | 张三   | 1990-01-01 00:00:00 |
| 002 | 李四   | 1990-02-02 00:00:00 |
+-----+--------+---------------------+

-- NOTE: 获取日期中的年份使用函数 year()


-- 查询 张老师 任课的学生成绩
mysql> select sno,score.cno,degree from score inner join course inner join teacher on score.cno=course.cno and course.tno=teacher.tno where tname="张老师";
+-----+-----+--------+
| sno | cno | degree |
+-----+-----+--------+
| 001 | 001 |     95 |
+-----+-----+--------+


-- 查询某选修课的同学人数多于1人的教师姓名
mysql> select tname from teacher inner join course inner join (select cno from score group by cno having count(sno) > 1) as  c on teacher.tno=course.tno and course.cno=c.cno;
+-----------+
| tname     |
+-----------+
| 赵老师    |
+-----------+

-- NOTE：先 gourp by 再使用 haing; 可以使用中间表进行连接查询


-- 查询存在有85分以上成绩的课程cno
mysql> select cno,degree from score where degree > 85;
+-----+--------+
| cno | degree |
+-----+--------+
| 001 |     95 |
| 002 |     90 |
+-----+--------+


-- 查询 计算机学院 教师所教课程的成绩表
mysql> select sno,course.cno,degree from score inner join course inner join teacher on score.cno=course.cno and course.tno=teacher.tno where depart="计算机学院";
+-----+-----+--------+
| sno | cno | degree |
+-----+-----+--------+
| 001 | 001 |     95 |
| 002 | 002 |     90 |
| 003 | 003 |     85 |
| 004 | 004 |     80 |
| 001 | 004 |     70 |
+-----+-----+--------+


-- 查询 计算机学院 与 艺术系不同职称教师的 tname, prof

-- 思路1：先找到相同的，然后取反
mysql> select tname,prof from teacher where prof not in (select prof from teacher where depart in ("计算机学院", "艺术系") group by prof having count(prof) >1);
+-----------+-----------+
| tname     | prof      |
+-----------+-----------+
| 张老师    | 副教授    |
| 赵老师    | 讲师      |
+-----------+-----------+


-- 思路2：先找到在计算机学院有的，然后排除在艺术系中也有的；然后采用同样的方式找到在艺术系有的，然后排除在计算机学院也有的；将最终结果合并到一起就是最终结果
mysql> select tname,prof from teacher where depart="计算机学院" and prof not in (select prof from teacher where depart="艺术系") union select tname,prof from teacher where depart="艺术系" and prof not in (select prof from teacher where depart="计算机学院");
+-----------+-----------+
| tname     | prof      |
+-----------+-----------+
| 张老师    | 副教授    |
| 赵老师    | 讲师      |
+-----------+-----------+


-- 启发: 同一个需求，不同的思路带来不同的解法

-- 新知识点： union 联合查询 - 取并集


-- 查询编号为001 课程且成绩至少高于选修编号为004的同学的 cno,sno,degree,并按 degree从高到低排序

-- 至少？ => 大于其中至少一个, any => 需要区分高于（使用max）和至少高于（使用any）这两种场景

mysql> select * from score where cno="001" and degree > any(select degree from score where cno="004") order by degree desc;
+-----+-----+--------+
| sno | cno | degree |
+-----+-----+--------+
| 001 | 001 |     95 |
+-----+-----+--------+


-- 查询课程001成绩高于课程004成绩的学生的 sno,degree
mysql> select a.sno,degree_001,degree_004 from (select sno,degree as degree_001 from score where cno="001") as a inner join (select sno, degree as degree_004 from score where cno="004") as b on a.sno=b.sno where degree_001 > degree_004;
+-----+------------+------------+
| sno | degree_001 | degree_004 |
+-----+------------+------------+
| 001 |         95 |         70 |
+-----+------------+------------+



-- 查询选修编号为 001 且 成绩高于选修编号为 004 课程的同学的 cno, sno , degree
mysql> select * from score where cno="001" and degree > all(select degree from score where cno="004");
+-----+-----+--------+
| sno | cno | degree |
+-----+-----+--------+
| 001 | 001 |     95 |
+-----+-----+--------+


-- 查询所有教师和同学的name,sex,birthday

-- 知识点：设置别名
mysql> select sname name,ssex sex,sbirthday birthday from student4 union (select tname,tsex,tbirthday from teacher);
+-----------+-----+---------------------+
| name      | sex | birthday            |
+-----------+-----+---------------------+
| 张三      | 男  | 1990-01-01 00:00:00 |
| 李四      | 女  | 1990-02-02 00:00:00 |
| 王五      | 男  | 1990-03-03 00:00:00 |
| 赵六      | 女  | 1990-04-04 00:00:00 |
| 张老师    | 男  | 1990-01-01 00:00:00 |
| 李老师    | 女  | 1990-02-02 00:00:00 |
| 王老师    | 男  | 1990-03-03 00:00:00 |
| 赵老师    | 女  | 1990-04-04 00:00:00 |
+-----------+-----+---------------------+

-- NOTE: union 关键词后面的列名不需要使用别名，默认会使用union前一部分的列名


-- 查询成绩被该课程平均成绩的同学的成绩表

-- 方法一：
mysql> select score.sno,score.cno,score.degree,avg_degree from score inner join (select cno,avg(degree) as avg_degree from score group by cno) a on score.cno=a.cno where degree < avg_degree;
+-----+-----+--------+------------+
| sno | cno | degree | avg_degree |
+-----+-----+--------+------------+
| 001 | 004 |     70 |    75.0000 |
+-----+-----+--------+------------+

-- 方法二：
mysql> select * from score a where a.degree < (select avg(degree) from score b where b.cno=a.cno);
+-----+-----+--------+
| sno | cno | degree |
+-----+-----+--------+
| 001 | 004 |     70 |
+-----+-----+--------+


-- 查询所有任课老师的 tname, depart
-- 隐含条件：在课程表中安排了课程的老师
mysql> select tname,depart from teacher where tno in (select distinct(tno) from course);
+-----------+-----------------+
| tname     | depart          |
+-----------+-----------------+
| 张老师    | 计算机学院      |
| 李老师    | 计算机学院      |
| 王老师    | 艺术系          |
| 赵老师    | 艺术系          |
+-----------+-----------------+


-- 查询至少有2名男生的班号
mysql> select class from student4 where ssex="男" group by class having count(*) >= 2;



-- 查询student4表中不姓王的同学记录： not like
mysql> select * from student4 where sname not like "王%";
+-----+--------+------+---------------------+--------+
| sno | sname  | ssex | sbirthday           | class  |
+-----+--------+------+---------------------+--------+
| 001 | 张三   | 男   | 1990-01-01 00:00:00 | 一班   |
| 002 | 李四   | 女   | 1990-02-02 00:00:00 | 二班   |
| 004 | 赵六   | 女   | 1990-04-04 00:00:00 | 四班   |
+-----+--------+------+---------------------+--------+


-- 查询student4表中每个同学的姓名和年龄：日期相减函数
mysql> select sname,(year(now()) - year(sbirthday)) age from student4;
+--------+------+
| sname  | age  |
+--------+------+
| 张三   |   32 |
| 李四   |   32 |
| 王五   |   32 |
| 赵六   |   32 |
+--------+------+


-- 查询student4表中最大和最小的 sbirthday 值
mysql> select max(sbirthday),min(sbirthday) from student4;
+---------------------+---------------------+
| max(sbirthday)      | min(sbirthday)      |
+---------------------+---------------------+
| 1990-04-04 00:00:00 | 1990-01-01 00:00:00 |
+---------------------+---------------------+


-- 以班号和年龄从大到小的顺序查询 student4 表中的全部记录
mysql> select * from student4 order by class desc, sbirthday;
+-----+--------+------+---------------------+--------+
| sno | sname  | ssex | sbirthday           | class  |
+-----+--------+------+---------------------+--------+
| 004 | 赵六   | 女   | 1990-04-04 00:00:00 | 四班   |
| 002 | 李四   | 女   | 1990-02-02 00:00:00 | 二班   |
| 003 | 王五   | 男   | 1990-03-03 00:00:00 | 三班   |
| 001 | 张三   | 男   | 1990-01-01 00:00:00 | 一班   |
+-----+--------+------+---------------------+--------+

-- NOTE：日期越早，年纪越大



-- 查询男教师及其所上的课程
mysql> select * from course where tno in (select tno from teacher where tsex="男");
+-----+-----------------+------+
| cno | cname           | tno  |
+-----+-----------------+------+
| 001 | 数据库原理      | 001  |
| 003 | 数据库系统      | 003  |
+-----+-----------------+------+


-- 查询最高分同学的 sno, cno , degree
mysql> select * from score  where degree = (select max(degree) from score);
+-----+-----+--------+
| sno | cno | degree |
+-----+-----+--------+
| 001 | 001 |     95 |
+-----+-----+--------+


-- 查询和“张三”同性别的所有同学的sname
mysql> select sname from student4 where ssex=(select ssex from student4 where sname="张三");
+--------+
| sname  |
+--------+
| 张三   |
| 王五   |
+--------+


-- 查询和“张三”同性别并同班的同学 sname

-- 方法一：
mysql> select sname from student4 where ssex= (select ssex from student4 where sname="张三") and class=(select class from student4 where sname="张三");
+--------+
| sname  |
+--------+
| 张三   |
+--------+


-- 方法二：新技巧 - 在 where 中使用多个列，用括号括起来
mysql> select sname from student4 where (ssex,class) = (select ssex,class from student4 where sname="张三");
+--------+
| sname  |
+--------+
| 张三   |
+--------+


-- 查询所有选修“数据库系统实践”课程的“男”同学的成绩表

mysql> select a.sno,a.cno,degree from score a inner join course b inner join student4 c on a.cno=b.cno and a.sno=c.sno where cname="数据库系统实践" and ssex="男";
+-----+-----+--------+
| sno | cno | degree |
+-----+-----+--------+
| 001 | 004 |     70 |
+-----+-----+--------+


-- 查询所有同学的 sno, cno, grade 列
mysql> select sno,cno,grade from score,grade where degree between low and upper;
+-----+-----+-------+
| sno | cno | grade |
+-----+-----+-------+
| 001 | 001 | A     |
| 002 | 002 | A     |
| 003 | 003 | B     |
| 004 | 004 | B     |
| 001 | 004 | C     |
+-----+-----+-------+

-- NOTE: 特别需要注意这个例子，之前想复杂了，原来两个没有任何字段可进行连接的表还能这样查询，长知识了



-- SQL 的4种连接查询

    -- 内连接： inner join 或者 join

    -- 外连接：
        -- 左连接：left join 或者 left outer join
        -- 右连接：right join 或者 right outer join
        -- 全连接：full join 或者 full outer join


-- person 表 
create table person(id int primary key, name varchar(20), cardId int);
insert into person values(1, '张三', 1), (2, '李四', 3), (3, '王五', 6);

-- card 表
create table card(id int primary key, name varchar(20));
insert into card values(1, '饭卡'), (2, '油卡'), (3, '银行卡'), (4, '会员卡');


-- NOTE：以上两张表无外键约束 （cardId=6 在card 表中不存在）

-- inner join 查询  => 返回两张表中同时满足指定条件的所有行
mysql> select * from person inner join card on person.cardId=card.id;
+----+--------+--------+----+-----------+
| id | name   | cardId | id | name      |
+----+--------+--------+----+-----------+
|  1 | 张三   |      1 |  1 | 饭卡      |
|  2 | 李四   |      3 |  3 | 银行卡    |
+----+--------+--------+----+-----------+


-- left join 查询 => 左边表返回全量数据，右边表满足条件的数据，右边表没有的数据用NULL填充
mysql> select * from person left join card on person.cardId=card.id;
+----+--------+--------+------+-----------+
| id | name   | cardId | id   | name      |
+----+--------+--------+------+-----------+
|  1 | 张三   |      1 |    1 | 饭卡      |
|  2 | 李四   |      3 |    3 | 银行卡    |
|  3 | 王五   |      6 | NULL | NULL      |
+----+--------+--------+------+-----------+


-- right join 查询 => 右边表返回全量数据，左边表满足条件的数据，左边表没有的数据用NULL填充
mysql> select * from person right join card on person.cardId=card.id;
+------+--------+--------+----+-----------+
| id   | name   | cardId | id | name      |
+------+--------+--------+----+-----------+
|    1 | 张三   |      1 |  1 | 饭卡      |
| NULL | NULL   |   NULL |  2 | 油卡      |
|    2 | 李四   |      3 |  3 | 银行卡    |
| NULL | NULL   |   NULL |  4 | 会员卡    |
+------+--------+--------+----+-----------+


-- full join 查询
mysql> select * from person full join card on person.cardId=card.id;
ERROR 1054 (42S22): Unknown column 'person.cardId' in 'on clause'

-- 原因：Oracle数据库支持full join，mysql是不支持full join的，但仍然可以同过左外连接+ union+右外连接实现

-- 替代方案：
mysql> select * from person left join card on person.cardId=card.id union select * from person right join card on person.cardId=card.id;
+------+--------+--------+------+-----------+
| id   | name   | cardId | id   | name      |
+------+--------+--------+------+-----------+
|    1 | 张三   |      1 |    1 | 饭卡      |
|    2 | 李四   |      3 |    3 | 银行卡    |
|    3 | 王五   |      6 | NULL | NULL      |
| NULL | NULL   |   NULL |    2 | 油卡      |
| NULL | NULL   |   NULL |    4 | 会员卡    |
+------+--------+--------+------+-----------+


-- union 和 union all 的区别 => 使用 union 会进行记录去重，使用 union all 不会进行记录去重

mysql> select * from person where id in (1,2) union select * from person where id in (2,3);
+----+--------+--------+
| id | name   | cardId |
+----+--------+--------+
|  1 | 张三   |      1 |
|  2 | 李四   |      3 |
|  3 | 王五   |      6 |
+----+--------+--------+

mysql> select * from person where id in (1,2) union all select * from person where id in (2,3);
+----+--------+--------+
| id | name   | cardId |
+----+--------+--------+
|  1 | 张三   |      1 |
|  2 | 李四   |      3 |
|  2 | 李四   |      3 |
|  3 | 王五   |      6 |
+----+--------+--------+
```

<br>

### mysql 事务
```sql
-- mysql 中，事务其实是一个最小的不可分割的工作单元。事务能保证业务的完整性

-- 比如银行转账：
-- A -> -100   => update account set balance = balance - 100 where id = 1
-- B -> +100   => update account set balance = balance + 100 where id = 2

-- 实际程序中，如果只有一条SQL执行成功，而另一个没有执行成功，就会出现数据前后不一致的情况
-- 多条SQL语句，可能会有同时执行成功的情况，要么就全部执行失败，不希望有部分执行成功部分不成功的情况出现，此时需要使用事务


-- mysql 中如何控制事务？

-- mysql 默认是开启事务的（自动提交）
mysql> select @@autocommit;
+--------------+
| @@autocommit |
+--------------+
|            1 |
+--------------+


-- 默认事务开启的作用是什么？
-- 当我们去执行一个sql语句的时候，效果就会立即体现出来，且不能回滚
-- 事务回滚：撤销SQL语句执行效果 - rollback


create database bank;

create table user(id int primary key, name varchar(20), money int);
insert into user values (1, '张三', 1000), (2, '李四', 2000), (3, '王五', 3000);


mysql> create table user(id int primary key, name varchar(20), money int);
Query OK, 0 rows affected (0.10 sec)

mysql> insert into user values (1, '张三', 1000), (2, '李四', 2000), (3, '王五', 3000);
Query OK, 3 rows affected (0.02 sec)
Records: 3  Duplicates: 0  Warnings: 0

mysql> rollback;
Query OK, 0 rows affected (0.00 sec)

mysql> select * from user;
+----+--------+-------+
| id | name   | money |
+----+--------+-------+
|  1 | 张三   |  1000 |
|  2 | 李四   |  2000 |
|  3 | 王五   |  3000 |
+----+--------+-------+

-- NOTE: 由于mysql默认开启了事务，所以插入数据后，执行回滚(rollback;) 命令不生效


-- 设置 mysql 自动提交为 false
mysql> set autocommit=0;
Query OK, 0 rows affected (0.01 sec)

mysql> select @@autocommit;
+--------------+
| @@autocommit |
+--------------+
|            0 |
+--------------+


-- 更新自动提交方式后，下面的回滚操作可执行成功

mysql> insert into user values (4, '赵六', 1000);
Query OK, 1 row affected (0.01 sec)

mysql> select * from user;
+----+--------+-------+
| id | name   | money |
+----+--------+-------+
|  1 | 张三   |  1000 |
|  2 | 李四   |  2000 |
|  3 | 王五   |  3000 |
|  4 | 赵六   |  1000 |
+----+--------+-------+
4 rows in set (0.00 sec)

mysql> rollback;
Query OK, 0 rows affected (0.01 sec)

mysql> select * from user;
+----+--------+-------+
| id | name   | money |
+----+--------+-------+
|  1 | 张三   |  1000 |
|  2 | 李四   |  2000 |
|  3 | 王五   |  3000 |
+----+--------+-------+


-- NOTE：将自动提交关闭后，执行SQL命令的时候，仅在抽象的虚拟表中生效，数据库真实表并未生效，需要手动显式提交才能生效 (显式手动提交后，rollback就不可能再撤销了，这个过程体现了事务的持久性)

mysql> insert into user values (4, '赵六', 1000);
Query OK, 1 row affected (0.00 sec)

mysql> commit;
Query OK, 0 rows affected (0.02 sec)

mysql> rollback;
Query OK, 0 rows affected (0.00 sec)

mysql> select * from user;
+----+--------+-------+
| id | name   | money |
+----+--------+-------+
|  1 | 张三   |  1000 |
|  2 | 李四   |  2000 |
|  3 | 王五   |  3000 |
|  4 | 赵六   |  1000 |
+----+--------+-------+


-- 模拟转账场景
mysql> update user set money=money-100 where name="张三";
Query OK, 1 row affected (0.03 sec)
Rows matched: 1  Changed: 1  Warnings: 0

mysql> update user set money=money+100 where name="李四";
Query OK, 1 row affected (0.02 sec)
Rows matched: 1  Changed: 1  Warnings: 0

mysql> select * from user;
+----+--------+-------+
| id | name   | money |
+----+--------+-------+
|  1 | 张三   |   900 |
|  2 | 李四   |  2100 |
|  3 | 王五   |  3000 |
|  4 | 赵六   |  1000 |
+----+--------+-------+

-- 当执行失败时，执行 rollback; 可恢复到原来的状态



-- 将自动提交方式恢复成默认值

mysql> set autocommit=1;
Query OK, 0 rows affected (0.00 sec)

mysql> select @@autocommit;
+--------------+
| @@autocommit |
+--------------+
|            1 |
+--------------+



-- 除 设置 autocommit 外，执行 begin; 或 start transaction; 都可以帮我们在不关闭自动提交时临时开启一个事务

mysql> select * from user;
+----+--------+-------+
| id | name   | money |
+----+--------+-------+
|  1 | 张三   |   900 |
|  2 | 李四   |  2100 |
|  3 | 王五   |  3000 |
|  4 | 赵六   |  1000 |
+----+--------+-------+
4 rows in set (0.00 sec)

mysql> begin;
Query OK, 0 rows affected (0.00 sec)

mysql> update user set money=money-100 where name="张三";
Query OK, 1 row affected (0.00 sec)
Rows matched: 1  Changed: 1  Warnings: 0

mysql> update user set money=money+100 where name="李四";
Query OK, 1 row affected (0.00 sec)
Rows matched: 1  Changed: 1  Warnings: 0

mysql> select * from user;
+----+--------+-------+
| id | name   | money |
+----+--------+-------+
|  1 | 张三   |   800 |
|  2 | 李四   |  2200 |
|  3 | 王五   |  3000 |
|  4 | 赵六   |  1000 |
+----+--------+-------+
4 rows in set (0.00 sec)

mysql> rollback;
Query OK, 0 rows affected (0.01 sec)

mysql> select * from user;
+----+--------+-------+
| id | name   | money |
+----+--------+-------+
|  1 | 张三   |   900 |
|  2 | 李四   |  2100 |
|  3 | 王五   |  3000 |
|  4 | 赵六   |  1000 |
+----+--------+-------+
4 rows in set (0.00 sec)


-- 事务开启之后，一旦 commit 提交，就不可以回滚（也就是当前的这个事务在提交的时候就结束了）


-- 事务隔离性：
    -- 1. read uncommitted;   读未提交的
    -- 2. read committed;     读提交的
    -- 3. repeatable read;    可以重复读 （mysql 默认隔离级别）
    -- 4. serializable;       串行化


-- 查看数据库的隔离级别

-- mysql 8.0
mysql> select @@global.transaction_isolation;
+--------------------------------+
| @@global.transaction_isolation |
+--------------------------------+
| REPEATABLE-READ                |
+--------------------------------+

mysql> select @@transaction_isolation;
+-------------------------+
| @@transaction_isolation |
+-------------------------+
| REPEATABLE-READ         |
+-------------------------+


-- mysql 5.x
select @@global.tx_isolation;
select @@tx_isolation;

-- NOTE: 带 global 前缀表示 系统级别，不带 global 前缀表示 会话级别




-- 修改隔离级别

mysql> set global transaction isolation level read uncommitted;
Query OK, 0 rows affected (0.00 sec)

mysql> select @@global.transaction_isolation;
+--------------------------------+
| @@global.transaction_isolation |
+--------------------------------+
| READ-UNCOMMITTED               |
+--------------------------------+


-- 第一种隔离级别：read uncommitted; - 如果有事务1和事务2，事务1对数据库进行操作，在操作过程中，事务没有被提交，但是事务2可以看到事务1操作的结果


-- 场景：李四 给张三 转账 100 元；李四 在上海的ATM上转账，张三 在成都的 ATM上查询账户

mysql> select * from user;
+----+--------+-------+
| id | name   | money |
+----+--------+-------+
|  1 | 张三   |   900 |
|  2 | 李四   |  2100 |
|  3 | 王五   |  3000 |
|  4 | 赵六   |  1000 |
+----+--------+-------+

-- 1.张三转账 100 元给李四
start transaction;
update user set money=money-100 where name="李四";
update user set money=money+100 where name="张三";

-- 2.李四查询账户
mysql> select * from user;
+----+--------+-------+
| id | name   | money |
+----+--------+-------+
|  1 | 张三   |  1000 |
|  2 | 李四   |  2000 |
|  3 | 王五   |  3000 |
|  4 | 赵六   |  1000 |
+----+--------+-------+

-- 李四发现账户金额显示转账成功（即自己的账户多了100元）
-- 此时如果手动执行了 rollback; 命令，上面的转账过程将被恢复至之前的状态，即转账不成功

-- 以上场景中，如果两个不同的地方，都在进行操作，如果事务1 开启之后，他的数据可以被其他事务读取到，这样就会出现 脏读

-- 脏读：一个事务读到了另一个事务没有提交的数据，就叫脏读（实际开发过程不允许脏读的情况出现）



-- 第二种隔离级别：read committed; 

mysql> set global transaction isolation level read committed;
Query OK, 0 rows affected (0.04 sec)

mysql> select @@global.transaction_isolation;
+--------------------------------+
| @@global.transaction_isolation |
+--------------------------------+
| READ-COMMITTED                 |
+--------------------------------+


-- 场景：
-- 小张为银行会计，正在进行数据统计操作，如下
mysql> begin;
Query OK, 0 rows affected (0.00 sec)

mysql> select * from user;
+----+--------+-------+
| id | name   | money |
+----+--------+-------+
|  1 | 张三   |   900 |
|  2 | 李四   |  2100 |
|  3 | 王五   |  3000 |
|  4 | 赵六   |  1000 |
+----+--------+-------+

-- 小张执行到完成后还未提交就去忙别的事了


-- 另一员工小李此时帮忙某用户新开了一个账户，如下
mysql> insert into user values(5,"孙七", 1000);
Query OK, 1 row affected (0.02 sec)

mysql> select * from user;
+----+--------+-------+
| id | name   | money |
+----+--------+-------+
|  1 | 张三   |   900 |
|  2 | 李四   |  2100 |
|  3 | 王五   |  3000 |
|  4 | 赵六   |  1000 |
|  5 | 孙七   |  1000 |
+----+--------+-------+

-- 之后小张回来继续操作，求金额平均值
mysql> select avg(money) from user;
+------------+
| avg(money) |
+------------+
|  1600.0000 |
+------------+


-- 发现与之前预期的 1750 不一致（因为后面新插入了一条记录导致数据表变更，但小张对此操作不可知）
-- 此时虽然我只能读到另外一个事务提交的数据，但还是会出现问题，就是读取同一个表的数据，发现前后不一致，此过程叫 不可重复读 现象


-- 第三种隔离级别：repeatable read;

mysql> set global transaction isolation level repeatable read;
Query OK, 0 rows affected (0.00 sec)

mysql> select @@global.transaction_isolation;
+--------------------------------+
| @@global.transaction_isolation |
+--------------------------------+
| REPEATABLE-READ                |
+--------------------------------+


-- 场景：

mysql> select * from user;
+----+--------+-------+
| id | name   | money |
+----+--------+-------+
|  1 | 张三   |   900 |
|  2 | 李四   |  2100 |
|  3 | 王五   |  3000 |
|  4 | 赵六   |  1000 |
+----+--------+-------+

-- 小张 - 成都 - 终端1
start transaction;

-- 小李 - 上海 - 终端2
start transaction;

-- 小张 - 成都 - 终端1
mysql> insert into user values (6,"d",900);
Query OK, 1 row affected (0.04 sec)

-- 小李 - 上海 - 终端2
mysql> select * from user;
+----+--------+-------+
| id | name   | money |
+----+--------+-------+
|  1 | 张三   |   900 |
|  2 | 李四   |  2100 |
|  3 | 王五   |  3000 |
|  4 | 赵六   |  1000 |
+----+--------+-------+

-- 小张 - 成都 - 终端1
mysql> commit;
Query OK, 0 rows affected (0.02 sec)

-- 小李 - 上海 - 终端2
mysql> select * from user;
+----+--------+-------+
| id | name   | money |
+----+--------+-------+
|  1 | 张三   |   900 |
|  2 | 李四   |  2100 |
|  3 | 王五   |  3000 |
|  4 | 赵六   |  1000 |
+----+--------+-------+

mysql> insert into user values (6,"d",900);
ERROR 1062 (23000): Duplicate entry '6' for key 'user.PRIMARY'


-- NOTE: 以上步骤种，虽然小李查询表时看不到数据，但和小张执行相同的sql语句时会提示数据已存在，这种现象叫 幻读

-- 幻读：事务1和事务2 同时操作同一张表，事务1提交的数据不能被数据2读到，就可以造成幻读现象



-- 第四种隔离级别：serializable;
mysql> set global transaction isolation level serializable;
Query OK, 0 rows affected (0.00 sec)

mysql> select @@global.transaction_isolation;
+--------------------------------+
| @@global.transaction_isolation |
+--------------------------------+
| SERIALIZABLE                   |
+--------------------------------+



-- 场景：

mysql> select * from user;
+----+--------+-------+
| id | name   | money |
+----+--------+-------+
|  1 | 张三   |   900 |
|  2 | 李四   |  2100 |
|  3 | 王五   |  3000 |
|  4 | 赵六   |  1000 |
+----+--------+-------+

-- 小张 - 成都 - 终端1
start transaction;


-- 小李 - 上海 - 终端2
start transaction;


-- 小张 - 成都 - 终端1
mysql> insert into user values (7,"e",900);
Query OK, 1 row affected (0.01 sec)

mysql> commit;
Query OK, 0 rows affected (0.01 sec)

mysql> select * from user;
+----+--------+-------+
| id | name   | money |
+----+--------+-------+
|  1 | 张三   |   900 |
|  2 | 李四   |  2100 |
|  3 | 王五   |  3000 |
|  4 | 赵六   |  1000 |
|  7 | e      |   900 |
+----+--------+-------+

-- 小李 - 上海 - 终端2
mysql> select * from user;
+----+--------+-------+
| id | name   | money |
+----+--------+-------+
|  1 | 张三   |   900 |
|  2 | 李四   |  2100 |
|  3 | 王五   |  3000 |
|  4 | 赵六   |  1000 |
|  7 | e      |   900 |
+----+--------+-------+


-- 小张 - 成都 - 终端1
mysql> start transaction;
Query OK, 0 rows affected (0.00 sec)

mysql> insert into user values (8,"f",900);
ERROR 1205 (HY000): Lock wait timeout exceeded; try restarting transaction


-- NOTE: 执行以上SQL命令后，命令卡住了 - 在 【小李 - 上海 - 终端2】 执行 commit 后，命令可执行成功

-- 小李 - 上海 - 终端2
mysql> commit;
Query OK, 0 rows affected (0.01 sec)


-- 小张 - 成都 - 终端1
mysql> insert into user values (8,"f",900);
Query OK, 1 row affected (0.00 sec)


-- 当 user 表被另一个事务操作时，其他事务里面的写操作是不允许进行的，进入排队状态（串行化），直到这个事务结束后（commit;），其他事务的写操作才可以成功


-- 串行化带来的问题是，性能特差

-- 性能排序(高 -> 低)： read-uncommitted > read-committed > repeatable-read > serializable   => 隔离级别越高，性能越差，mysql 默认隔离级别是：repeatable-read


-- 事务的4大特征（ACID）
    A: 原子性 - 事务是最小的单位，不可以再分割
    C: 一致性 - 事务要求，同一事务中的SQL语句，必须保证同时成功或同时失败
    I: 隔离性 - 事务1 和 事务2 之间是具有隔离性的
    D: 持久性 - 事务一旦结束，就不可返回


-- 事务开启的几种方式：
    -- 1.修改mysql默认提交方式：set autocommit=0;
    -- 2.使用 begin;
    -- 3.使用 start transaction;

-- 事务提交：
commit;


-- 事务手动回滚：
rollback;
```

<br><br>