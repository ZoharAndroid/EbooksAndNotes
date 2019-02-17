<p align = "center">MySQL必知必会 阅读笔记</p>
<p align = "right">zohar.zzh</p>
<p align = "right">2019-2-12</p>

[toc]

# 第1章 了解SQL

这一章节讲解了基本的概念，比如：数据库、表、列（column）、数据类型、行（row）、主键。基本了解即可。

* 数据库：保存有组织的数据的容器。
* 表：某种特定类型数据的结构化清单。
* 模式（scheme）：关于数据库和表的布局以及特性的信息。
* 列：表中的一个字段。所有表都是由一列或者多列组成。
* 数据类型：数据的类型。
* 行：表中的一个记录。
* 主键：一列（或者一组列）其值能够唯一区别表中的每个行。

# 第2章 MySQL简介

介绍了MySQL和MySQL工具（命令行、MySQL Administrator、MySQL Query Browser）。后面两个工具都是图形化界面，前者是管理，后者是能够编写和执行SQL命令。

# 第21章 创建和操作数据库和表

这本书的把创建表写在后面了，所以就把创建数据库和表写在前面来。

## 21.0 创建数据库

1. 创建数据库
   
```
    create database crashcourse;
```

2. 切换到该数据库
```
    use crashcourse;
```

## 21.1 创建表

使用crate table 表名();

* 创建customer表
```
create table customers(
       cust_id int not null auto_increment,
       cust_name char(50) not null,
       cust_address char(50) null,
       cust_city char(50) null,
       cust_state char(5) null,
       cust_zip char(10) null,
       cust_country char(50) null,
       cust_contact char(50) null,
       cust_email char(255) null,
       primary key (cust_id)
       )engine=InnoDB;
```
> 需要理解的部分：
> 1. auto_inrement：就是自增长，每个表只能允许1列使用auto_increment，而且必须被索引（如：通过成为主键）；
> 2. primary key(cust_id)：让该列成为主键;
> 3. engine=InnoDB：设置引擎类型，可选的引擎类型：InnoDB（可靠事务处理引擎，但是不支持全文本搜索）、MEMORY(功能等同与MyISAM,数据存储于内存，速度快，适用于临时表)、MyISAM（支持全文本搜索，但不支持事务处理）。


* 创建order表
```
create table orders(
     order_num int not null auto_increment,
     order_date datetime not null,
     cust_id int not null,
     primary key (order_num)
     )engine = InnoDB;
```
> 需要注意的部分：
> 1. datetime数据类型：是date和time的组合。

  
* 创建vendor表
```
 create table vendors(
     vend_id int not null auto_increment,
     vend_name char(50) not null,
     vend_address char(50) null,
     vend_city char(50) null,
     vend_state char(5) null,
     vend_zip char(10) null,
     vend_country char(50) null,
     primary key (vend_id)
     )engine=InnoDB;
```

* 创建orderitems表
```
create table orderitems(
     order_num int not null,
     order_item int not null,
     prod_id char(10) not null,
     quantity int not null,
     item_price decimal(8,2) not null,
     primary key (order_num,order_item)
     )engine=InnoDB;
```
> 注意部分：
> 1. 多个列组成一个主键的格式，用逗号隔开；
> 2. 设置默认值，比如这个表格中的quantity列设置：
>   ```quantity int not null default 1```
>   这个就是设置默认值为1。

* 创建product表
```
create table produces(
     prod_id char(10) not null,
     vend_id int not null,
     prod_name char(255) not null,
     prod_price decimal(8,2) not null,
     prod_desc text null,
     primary key (prod_id)
     )engine=InnoDB;
```
> 注意部分：
> 1. text数据类型：最大长度为64K的变长文本。

* 创建productnotes表
```
create table productnotes(
     note_id int not null auto_increment,
     prod_id char(10) not null,
     note_date datetime not null,
     note_text text null,
     primary key(note_id),
     fulltext(note_text)
     )engine = MyISAM;
```
> 需要注意的部分
> 1. fulltext(note_text)：全文本搜索。
> 2. 引擎类型设置为：MyISAM.

## 21.2 更新表

使用alter table语句。

* 给表添加一列：
```
alter table vendors add vend_phone char(20);
```
> 需要注意的部分：
> 1. 更新表用alter table；
> 2. 给一个表增加一列的格式为： add 列名 数据类型；

* 给表删除一列：
```
alter table vendors drop column vend_phone;
```
> 需要注意的部分：
> 1. 删除表中的一列格式为：drop column 列名；

* 设置表的外键（alter table的常见用途）
```
alter table orderitems add constraint fk_orderitems_orders foreign key(order_num) references orders(order_num);

alter table orderitems add constraint fk_orderitems_products foreign key(prod_id) references products(prod_id);

alter table orders add constraint fk_orders_customers foreign key(cust_id) references customers(cust_id);

alter table products add constraint fk_products_vendors foreign key(vend_id) references vendors(vend_id);
```
> 需要注意的部分：
> 1. 设置外键格式：alter table <表名> add constraint <限制名称> foreign key(外键列) references <外表（外键列)>;

* 重命名表
```
rename table <表名称> to <新表名称>;
```

* 重命名多个表
```
rename table <表名称1> to <新表名称1>,<表名称2> to <新表名称2>；
```

* 删除表
```
drop table <表名称>；
```

# 第3章 使用MySQL

* 显示数据库
```
输入：show databases;

输出：
+--------------------+
| Database           |
+--------------------+
| information_schema |
| crashcourse        |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
5 rows in set (0.00 sec)
```

* 显示表
```
输入：show tables;

输出：
+-----------------------+
| Tables_in_crashcourse |
+-----------------------+
| customers             |
| orderitems            |
| orders                |
| productnotes          |
| products              |
| vendors               |
+-----------------------+
6 rows in set (0.00 sec)
```

* 显示表列：
```
输入：show columns from <表名称>;

输出：
+--------------+-----------+------+-----+---------+----------------+
| Field        | Type      | Null | Key | Default | Extra          |
+--------------+-----------+------+-----+---------+----------------+
| cust_id      | int(11)   | NO   | PRI | NULL    | auto_increment |
| cust_name    | char(50)  | NO   |     | NULL    |                |
| cust_address | char(50)  | YES  |     | NULL    |                |
| cust_city    | char(50)  | YES  |     | NULL    |                |
| cust_state   | char(5)   | YES  |     | NULL    |                |
| cust_zip     | char(10)  | YES  |     | NULL    |                |
| cust_country | char(50)  | YES  |     | NULL    |                |
| cust_contact | char(50)  | YES  |     | NULL    |                |
| cust_email   | char(255) | YES  |     | NULL    |                |
+--------------+-----------+------+-----+---------+----------------+
9 rows in set (0.00 sec)
```
# 第19章 插入数据

* 插入完整的行
```
insert into <表名称> values(null,'zohar.zzh','90046',null,null);
```
> 需要注意的部分：
> 1. 插入数据用insert into;
> 2. 这个格式只列出表名称，所以给出的values(x,x,x)需要与表给出的顺序一致；
> 3. 加入第一列为主键且设置为auto_increment,那么第一列的数据值可以设置为null。当你不想给出或者设置这个值的时候，就可以按照直接写成null这种方法来处理。

```
insert into <customers(cust_name,cust_address,cust_city,cust_state,cust_zip,cust_country,cust_contact,cust_email)> values(x,x,x,x,x,...); 
```
> 需要注意的部分：
> 1. 列出了部分列数。

* 出入多行数据
```
insert into <表名称(x,x,x)> values (x,x,x),(x,x,x),(x,x,x);
```
> 注意点：
> 1. 插入多行数据就是在values后面用逗号，进行分隔。

# 第4章 检索数据

* 查询单列
```
输入：select prod_name from products;

输出：
+----------------+
| prod_name      |
+----------------+
| .5 ton anvil   |
| 1 ton anvil    |
| 2 ton anvil    |
| Detonator      |
| Bird seed      |
| Carrots        |
| Fuses          |
| JetPack 1000   |
| JetPack 2000   |
| Oil can        |
| Safe           |
| Sling          |
| TNT (1 stick)  |
| TNT (5 sticks) |
+----------------+
```

* 查询多列
```
输入：select prod_id,prod_name,prod_price from products;

输出：
+---------+----------------+------------+
| prod_id | prod_name      | prod_price |
+---------+----------------+------------+
| ANV01   | .5 ton anvil   |       5.99 |
| ANV02   | 1 ton anvil    |       9.99 |
| ANV03   | 2 ton anvil    |      14.99 |
| DTNTR   | Detonator      |      13.00 |
| FB      | Bird seed      |      10.00 |
| FC      | Carrots        |       2.50 |
| FU1     | Fuses          |       3.42 |
| JP1000  | JetPack 1000   |      35.00 |
| JP2000  | JetPack 2000   |      55.00 |
| OL1     | Oil can        |       8.99 |
| SAFE    | Safe           |      50.00 |
| SLING   | Sling          |       4.49 |
| TNT1    | TNT (1 stick)  |       2.50 |
| TNT2    | TNT (5 sticks) |      10.00 |
+---------+----------------+------------+
```

* 查询所有数据
```
select * from products;
```

* 查询出不同的行（不显示重复的行）
```
mysql> select vend_id from products;
+---------+
| vend_id |
+---------+
|    1001 |
|    1001 |
|    1001 |
|    1002 |
|    1002 |
|    1003 |
|    1003 |
|    1003 |
|    1003 |
|    1003 |
|    1003 |
|    1003 |
|    1005 |
|    1005 |
+---------+
```
>  这里可以看到有重复的值，如何消除重复呢？使用distinct
```
mysql> select distinct vend_id from products;
+---------+
| vend_id |
+---------+
|    1001 |
|    1002 |
|    1003 |
|    1005 |
+---------+
```
> 当distinct应用于多个列，只有当多个列中的内容都相同，才会不显示，否则都会显示出来。
> ```
> mysql> select distinct vend_id,prod_price from products;
>+---------+------------+
>| vend_id | prod_price |
>+---------+------------+
>|    1001 |       5.99 |
>|    1001 |       9.99 |
>|    1001 |      14.99 |
>|    1003 |      13.00 |
>|    1003 |      10.00 |
>|    1003 |       2.50 |
>|    1002 |       3.42 |
>|    1005 |      35.00 |
>|    1005 |      55.00 |
>|    1002 |       8.99 |
>|    1003 |      50.00 |
>|    1003 |       4.49 |
>+---------+------------+
> ```
> 这里就是一个列子，如果这里有一列不同，就会显示出来。

* 限制结果
```
mysql> select prod_name from products limit 5;
+--------------+
| prod_name    |
+--------------+
| .5 ton anvil |
| 1 ton anvil  |
| 2 ton anvil  |
| Detonator    |
| Bird seed    |
+--------------+
```
> 注意： 
> 1. limit 5：从起始的0行到第4行，总共5行。
> 2. 起始行是从0开始的。

```
mysql> select prod_name from products limit 5,5;
+--------------+
| prod_name    |
+--------------+
| Carrots      |
| Fuses        |
| JetPack 1000 |
| JetPack 2000 |
| Oil can      |
+--------------+
```
> 注意：
> 1. limit 5,5：第一个数是起始行，第二个数是偏移数
> 2. 当第2个数大于表中所有行的总数时，有多少行就列出多少行。
> e.g. 
> ```
>mysql> select prod_name from products limit 10,5;
>+----------------+
>| prod_name      |
>+----------------+
>| Safe           |
>| Sling          |
>| TNT (1 stick)  |
>| TNT (5 sticks) |
>+----------------+
>```
> 只显示了4行。
> 3. 当第一个数都大于表中的所有行时，就直接输出空的结果。
> e.g.
>```
>mysql> select prod_name from products limit 14,5;
>Empty set (0.00 sec)
>```

* 使用完全限定的表名（列前面用表名限定）
```
mysql> select products.prod_name from products;
```
* 表名也可以完全限定的名称(表名前用数据库名限定)
```
mysql> select products.prod_name from crashcourse.products;
```
# 第5章 排序查询数据(order by)

* 排序数据
```
mysql> select prod_name from products order by prod_name;
+----------------+
| prod_name      |
+----------------+
| .5 ton anvil   |
| 1 ton anvil    |
| 2 ton anvil    |
| Bird seed      |
| Carrots        |
| Detonator      |
| Fuses          |
| JetPack 1000   |
| JetPack 2000   |
| Oil can        |
| Safe           |
| Sling          |
| TNT (1 stick)  |
| TNT (5 sticks) |
+----------------+
```
> 默认按照升序进行排列（ASC）。

* 按照多个列排序
```
mysql> select prod_id,prod_price,prod_name from products order by prod_price,prod_name;
+---------+------------+----------------+
| prod_id | prod_price | prod_name      |
+---------+------------+----------------+
| FC      |       2.50 | Carrots        |
| TNT1    |       2.50 | TNT (1 stick)  |
| FU1     |       3.42 | Fuses          |
| SLING   |       4.49 | Sling          |
| ANV01   |       5.99 | .5 ton anvil   |
| OL1     |       8.99 | Oil can        |
| ANV02   |       9.99 | 1 ton anvil    |
| FB      |      10.00 | Bird seed      |
| TNT2    |      10.00 | TNT (5 sticks) |
| DTNTR   |      13.00 | Detonator      |
| ANV03   |      14.99 | 2 ton anvil    |
| JP1000  |      35.00 | JetPack 1000   |
| SAFE    |      50.00 | Safe           |
| JP2000  |      55.00 | JetPack 2000   |
+---------+------------+----------------+
```
> 1. 按照多个列进行排序也就是用逗号隔开。
> 2. 多个列排序的显示规则:order by后的第一个列是主要的排列顺序，第二个列，是在第一个列中有相同的数据中进行排序，以此类推，第三个列实在第二个列有相同数据中进行排序。

* 指定排序方向
```
mysql> select prod_id,prod_price,prod_name from products order by prod_price desc;
+---------+------------+----------------+
| prod_id | prod_price | prod_name      |
+---------+------------+----------------+
| JP2000  |      55.00 | JetPack 2000   |
| SAFE    |      50.00 | Safe           |
| JP1000  |      35.00 | JetPack 1000   |
| ANV03   |      14.99 | 2 ton anvil    |
| DTNTR   |      13.00 | Detonator      |
| FB      |      10.00 | Bird seed      |
| TNT2    |      10.00 | TNT (5 sticks) |
| ANV02   |       9.99 | 1 ton anvil    |
| OL1     |       8.99 | Oil can        |
| ANV01   |       5.99 | .5 ton anvil   |
| SLING   |       4.49 | Sling          |
| FU1     |       3.42 | Fuses          |
| FC      |       2.50 | Carrots        |
| TNT1    |       2.50 | TNT (1 stick)  |
+---------+------------+----------------+
```
> 1. desc:为降序，asc为升序（默认）。

* 多列指定排序方向
```
mysql> select prod_id,prod_price,prod_name from products order by prod_price desc,prod_name asc;
+---------+------------+----------------+
| prod_id | prod_price | prod_name      |
+---------+------------+----------------+
| JP2000  |      55.00 | JetPack 2000   |
| SAFE    |      50.00 | Safe           |
| JP1000  |      35.00 | JetPack 1000   |
| ANV03   |      14.99 | 2 ton anvil    |
| DTNTR   |      13.00 | Detonator      |
| FB      |      10.00 | Bird seed      |
| TNT2    |      10.00 | TNT (5 sticks) |
| ANV02   |       9.99 | 1 ton anvil    |
| OL1     |       8.99 | Oil can        |
| ANV01   |       5.99 | .5 ton anvil   |
| SLING   |       4.49 | Sling          |
| FU1     |       3.42 | Fuses          |
| FC      |       2.50 | Carrots        |
| TNT1    |       2.50 | TNT (1 stick)  |
+---------+------------+----------------+
```
> 1. prod_price指定为降序，prod_name指定为升序。
> 2. 如果是多个列，需要对每个列制定顺序，asc可以省略，因为默认。

* order by与limit组合找到最大最小数据
```
mysql> select prod_price from products order by prod_price desc limit 1;
+------------+
| prod_price |
+------------+
|      55.00 |
+------------+
```
> 1. 找到最大的价格数据，prod_price 设置为降序，limit显示输出1个数据，也就是最大的数据。
> 2. 注意order by 和limit的位置，order by 在 from 之后,limit 之前。limit在order by之后。

# 第6章 过滤数据(where)

* 检索出指定的值的数据
1. =的使用
```
mysql> select prod_name,prod_price from products where prod_name = "fuses";
+-----------+------------+
| prod_name | prod_price |
+-----------+------------+
| Fuses     |       3.42 |
+-----------+------------+
```
> 1. 检索出prod_name的值为“fuses”的数据。
> 2. where的位置位于from之后，如果有order by语句，那么order by 位于where之后。
> 3. mysql默认不区分大小写。如果要区分，需要DBA去设置。
> 4. 字符串数据用“ ”。

2. <的使用
```
mysql> select prod_name,prod_price from products where prod_price < 10;
+---------------+------------+
| prod_name     | prod_price |
+---------------+------------+
| .5 ton anvil  |       5.99 |
| 1 ton anvil   |       9.99 |
| Carrots       |       2.50 |
| Fuses         |       3.42 |
| Oil can       |       8.99 |
| Sling         |       4.49 |
| TNT (1 stick) |       2.50 |
+---------------+------------+
```
> 1. 检索出价格小于10元的数据

3. <=的使用
```
mysql> select prod_name,prod_price from products where prod_price <= 10;
+----------------+------------+
| prod_name      | prod_price |
+----------------+------------+
| .5 ton anvil   |       5.99 |
| 1 ton anvil    |       9.99 |
| Bird seed      |      10.00 |
| Carrots        |       2.50 |
| Fuses          |       3.42 |
| Oil can        |       8.99 |
| Sling          |       4.49 |
| TNT (1 stick)  |       2.50 |
| TNT (5 sticks) |      10.00 |
+----------------+------------+
```
> 1. 检索出价格小于等于10的数据

4. <>/!= 的使用
```
mysql> select vend_id,prod_price from products where vend_id <> 1003;
+---------+------------+
| vend_id | prod_price |
+---------+------------+
|    1001 |       5.99 |
|    1001 |       9.99 |
|    1001 |      14.99 |
|    1002 |       3.42 |
|    1005 |      35.00 |
|    1005 |      55.00 |
|    1002 |       8.99 |
+---------+------------+
```
> 1. 检索出vend_id不等于1003的数据。
> 2. 将<>修改成!=的效果是一样的。

5. between的使用
```
mysql> select prod_name,prod_price from products where prod_price between 5 and 10;
+----------------+------------+
| prod_name      | prod_price |
+----------------+------------+
| .5 ton anvil   |       5.99 |
| 1 ton anvil    |       9.99 |
| Bird seed      |      10.00 |
| Oil can        |       8.99 |
| TNT (5 sticks) |      10.00 |
+----------------+------------+
```
> 1. 检索出价格为5到10之间的数据。
> 2. between需要两个数据，且两个数据用and进行连接。

* 空值检查
```
mysql> select cust_id from customers where cust_email is null;
+---------+
| cust_id |
+---------+
|   10002 |
|   10005 |
+---------+
```
> 1. 检查customers表中的email是否为null。
> 2. 用is null来判断。

# 第7章 数据过滤（where通过and/or组合，in/not操作符的使用）

* and操作符
```
mysql> select vend_id,prod_price,prod_name from products where vend_id=1003 and prod_price<=10;
+---------+------------+----------------+
| vend_id | prod_price | prod_name      |
+---------+------------+----------------+
|    1003 |      10.00 | Bird seed      |
|    1003 |       2.50 | Carrots        |
|    1003 |       4.49 | Sling          |
|    1003 |       2.50 | TNT (1 stick)  |
|    1003 |      10.00 | TNT (5 sticks) |
+---------+------------+----------------+
```
> 1. 查询出vend_id = 1003 并且价格小于等于10的数据。
> 2. 每多添加一个条件就用and进行连接。

* or操作符
```
mysql> select vend_id,prod_name from products where vend_id = 1002 or vend_id = 1003;
+---------+----------------+
| vend_id | prod_name      |
+---------+----------------+
|    1003 | Detonator      |
|    1003 | Bird seed      |
|    1003 | Carrots        |
|    1002 | Fuses          |
|    1002 | Oil can        |
|    1003 | Safe           |
|    1003 | Sling          |
|    1003 | TNT (1 stick)  |
|    1003 | TNT (5 sticks) |
+---------+----------------+
```
> 1. 找出vend_id = 1002 或者 1003的数据。

* and和or操作符的优先级
```
mysql> select prod_name,prod_price from products where (vend_id = 1002 or vend_id = 1003) and prod_price >= 10;
+----------------+------------+
| prod_name      | prod_price |
+----------------+------------+
| Detonator      |      13.00 |
| Bird seed      |      10.00 |
| Safe           |      50.00 |
| TNT (5 sticks) |      10.00 |
+----------------+------------+
```
> 1. 找出vend_id = 1002 或者 1003 并且价格大于等于10的数据。
> 2. 注意使用括号调整优先级，and优先级最高，如果不加括号，输出的结果会不同。

* in操作符
```
mysql> select vend_id,prod_name,prod_price from products where vend_id in (1001,1002,1003) order by prod_name;
+---------+----------------+------------+
| vend_id | prod_name      | prod_price |
+---------+----------------+------------+
|    1001 | .5 ton anvil   |       5.99 |
|    1001 | 1 ton anvil    |       9.99 |
|    1001 | 2 ton anvil    |      14.99 |
|    1003 | Bird seed      |      10.00 |
|    1003 | Carrots        |       2.50 |
|    1003 | Detonator      |      13.00 |
|    1002 | Fuses          |       3.42 |
|    1002 | Oil can        |       8.99 |
|    1003 | Safe           |      50.00 |
|    1003 | Sling          |       4.49 |
|    1003 | TNT (1 stick)  |       2.50 |
|    1003 | TNT (5 sticks) |      10.00 |
+---------+----------------+------------+
```
> 1. 找出vend_id为1001，1002，1003的所有值。

* not in操作符
```
mysql> select vend_id,prod_name from products where vend_id not in (1002,1003);
+---------+--------------+
| vend_id | prod_name    |
+---------+--------------+
|    1001 | .5 ton anvil |
|    1001 | 1 ton anvil  |
|    1001 | 2 ton anvil  |
|    1005 | JetPack 1000 |
|    1005 | JetPack 2000 |
+---------+--------------+
```
> 1. 找出vend_id不在1002和1003的数据。

# 第8章 用通配符进行过滤（like操作符，%和_通配符）

* %通配符
```
mysql> select prod_id,prod_name from products where prod_name like "%anvil%";
+---------+--------------+
| prod_id | prod_name    |
+---------+--------------+
| ANV01   | .5 ton anvil |
| ANV02   | 1 ton anvil  |
| ANV03   | 2 ton anvil  |
+---------+--------------+
```
> 1. 查询包含"anvil“的数据。
> 2. %就是包含任意个数的字符。

* _通配符
```
mysql> select prod_id,prod_name from products where prod_name like "_ ton anvil";
+---------+-------------+
| prod_id | prod_name   |
+---------+-------------+
| ANV02   | 1 ton anvil |
| ANV03   | 2 ton anvil |
+---------+-------------+
```
> 1. 找出prod_name的字符串为”x ton anvil"的数据。
> 2. _通配符表示一个字符。

# 第9章 正则表达式进行搜索（regexp)
* 匹配基本字符
```
mysql> select prod_name from products where prod_name regexp ".000";
+--------------+
| prod_name    |
+--------------+
| JetPack 1000 |
| JetPack 2000 |
+--------------+
```
> 1. 匹配prod_name中包含的有x000的。.表示一个字符.

* 进行or匹配
```
mysql> select prod_name from products where prod_name regexp '1000|2000';
+--------------+
| prod_name    |
+--------------+
| JetPack 1000 |
| JetPack 2000 |
+--------------+
```
> 1. 匹配prod_name中包含的有1000和2000的数据。
> 2. |是正则表达式的or操作符.

* 匹配几个字符之一
```
mysql> select prod_name from products where prod_name regexp '[123] ton';
+-------------+
| prod_name   |
+-------------+
| 1 ton anvil |
| 2 ton anvil |
+-------------+
```
> 1. [123]是匹配1或者2或者3。
> 2. 效果同'[1|2|3] ton'。‘|’可以省略。

需要注意要用[]来定义，否则会出现问题：
```
mysql> select prod_name from products where prod_name regexp '1|2|3 ton';
+---------------+
| prod_name     |
+---------------+
| 1 ton anvil   |
| 2 ton anvil   |
| JetPack 1000  |
| JetPack 2000  |
| TNT (1 stick) |
+---------------+
```
> 1. 这里没有用[]括起来，要查询的结果也就不正确了。这里查询的结果是包含'1','2'和‘3 tom'的字符串数据。

* 匹配范围
```
mysql> select prod_name from products where prod_name regexp '[0-5] ton';
+--------------+
| prod_name    |
+--------------+
| .5 ton anvil |
| 1 ton anvil  |
| 2 ton anvil  |
+--------------+
```
> 1. [0-5]匹配0至5的任意字符。
> 2. [a-z] : 任意小写字符；[A-Z]:任意大写字符;[0-9]：数字字符。

* 匹配特殊字符
```
mysql> select vend_name  from vendors where vend_name regexp '\\.';
+--------------+
| vend_name    |
+--------------+
| Furball Inc. |
+--------------+
```
> 1. '\\\\.'匹配带有.的数据。
> 2. mysql转移字符用双斜杠来表示。

* 匹配多个实例

重复元字符|说明
|:-:|:-:|
*|0个或多个匹配
+|1个或多个匹配(等于{1,})
?|0个或1个匹配(等于{0,1})
{n}|指定数目的匹配
{n,}|不少于指定数目的匹配
{n,m}|匹配数目范围

```
mysql> select prod_name from products where prod_name regexp '\\([0-9] sticks?\\)';
+----------------+
| prod_name      |
+----------------+
| TNT (1 stick)  |
| TNT (5 sticks) |
+----------------+
```
> 1.匹配(数字 stick/sticks)的数据。\\\\(是转移字符，s?表示的是s字符有0个或者1个。

```
mysql> select prod_name from products where prod_name regexp '[0-9]{4}';
+--------------+
| prod_name    |
+--------------+
| JetPack 1000 |
| JetPack 2000 |
+--------------+
```
> 1. [0-9]{4}表示4个任意数字字符。所以这条查询算法就是找出包含4个数字的数据。

* 定位符
^：表示文本的开头；$：表示文本的结尾。
```
mysql> select prod_name from products where prod_name regexp '^[0-9\\.]';
+--------------+
| prod_name    |
+--------------+
| .5 ton anvil |
| 1 ton anvil  |
| 2 ton anvil  |
+--------------+
```
> 1. \^[0-9\\.] ： 表示找出文本开始为数字或者.的数据。

# 第10章 创建计算字段(拼接字段，使用别名，执行算术计算)

* 拼接字段
```
mysql> select concat(vend_name,'(',vend_country,')') from vendors;
+----------------------------------------+
| concat(vend_name,'(',vend_country,')') |
+----------------------------------------+
| Anvils R Us(USA)                       |
| LT Supplies(USA)                       |
| ACME(USA)                              |
| Furball Inc.(USA)                      |
| Jet Set(England)                       |
| Jouets Et Ours(France)                 |
+----------------------------------------+
```
> 1. 利用concat()函数设置输出结果。
> 2. concat()：字符串拼接函数，函数中子字符串用的逗号进行分隔。

* 消除前后空格
```
mysql> select concat(trim(vend_name),'(',vend_country,')') from vendors;
+----------------------------------------------+
| concat(trim(vend_name),'(',vend_country,')') |
+----------------------------------------------+
| Anvils R Us(USA)                             |
| LT Supplies(USA)                             |
| ACME(USA)                                    |
| Furball Inc.(USA)                            |
| Jet Set(England)                             |
| Jouets Et Ours(France)                       |
+----------------------------------------------+
```
> 1. 利用trim()函数消除左右空格。
> 2. rtrim()和ltrim()分别是消除右空格和左空格。

* 使用别名
```
mysql> select concat(trim(vend_name),'(',vend_country,')') as vend_title from vendors;
+------------------------+
| vend_title             |
+------------------------+
| Anvils R Us(USA)       |
| LT Supplies(USA)       |
| ACME(USA)              |
| Furball Inc.(USA)      |
| Jet Set(England)       |
| Jouets Et Ours(France) |
+------------------------+
```
> 1. 格式为：select 列 as 别名 from 表名;

* 执行算术计算
```
mysql> select prod_id,quantity,item_price,quantity*item_price as price from orderitems where order_num = 20005;
+---------+----------+------------+-------+
| prod_id | quantity | item_price | price |
+---------+----------+------------+-------+
| ANV01   |       10 |       5.99 | 59.90 |
| ANV02   |        3 |       9.99 | 29.97 |
| TNT2    |        5 |      10.00 | 50.00 |
| FB      |        1 |      10.00 | 10.00 |
+---------+----------+------------+-------+
```
> 1. 第4列为数量和单价相乘的结果。（ + - * /）都可以。
> 2. 利用了as作为第4列别名。

# 第11章 使用数据处理函数

## 11.1 文本处理函数
函数|说明|
|:-:|:-:|
left()|返回串最左边的字符
right()|返回串最右边的字符
length()|返回串的长度
lower()|转换成小写
upper()|转换成大写
ltrim()|删除左边空格|
trim()|删除左右边空格
rtrim()|删除右边空格
locate()|找出一个子串
substring()|返回子串的字符
soundex()|返回串的发音值

* upper()函数的使用
```
mysql> select vend_name,upper(vend_name) as upper_name from vendors;
+----------------+----------------+
| vend_name      | upper_name     |
+----------------+----------------+
| Anvils R Us    | ANVILS R US    |
| LT Supplies    | LT SUPPLIES    |
| ACME           | ACME           |
| Furball Inc.   | FURBALL INC.   |
| Jet Set        | JET SET        |
| Jouets Et Ours | JOUETS ET OURS |
+----------------+----------------+
```
> 1. 第二列变成大写

* soundex()函数的使用
```
mysql> select cust_name,cust_contact from customers where soundex(cust_contact) = soundex('y lie');
+-------------+--------------+
| cust_name   | cust_contact |
+-------------+--------------+
| Coyote Inc. | Y Lee        |
+-------------+--------------+
```
> 1. soundex()函数是判断两者的发音是否相同，比如数据库中的为Y Lee与输出的y lie两者发音相同。

## 11.2 数值处理函数
函数|说明|
|:-:|:-:|
abs()|绝对值|
cos()|余弦|
sin()|正弦
tan()|正切
pi()|pi
exp()|指数值
mod()|余数
sqrt()|平方根

## 11.3 日期和时间处理函数
![](https://raw.githubusercontent.com/ZoharAndroid/MarkdownImages/master/%E5%B8%B8%E7%94%A8%E6%97%B6%E9%97%B4%E5%92%8C%E6%97%A5%E6%9C%9F%E5%A4%84%E7%90%86%E5%87%BD%E6%95%B0.png)

* date()函数的使用
```
mysql> select cust_id,order_num,order_date from orders where date(order_date)='2005-09-01';
+---------+-----------+---------------------+
| cust_id | order_num | order_date          |
+---------+-----------+---------------------+
|   10001 |     20005 | 2005-09-01 00:00:00 |
+---------+-----------+---------------------+
```
> 1. date()函数查询抽取出日期来进行比较。

* 检索出某个月的数据处理方法？
1. 方法1：
```
mysql> select cust_id,order_num,order_date from orders where date(order_date) between '2005-09-01' and '2005-09-30';
+---------+-----------+---------------------+
| cust_id | order_num | order_date          |
+---------+-----------+---------------------+
|   10001 |     20005 | 2005-09-01 00:00:00 |
|   10003 |     20006 | 2005-09-12 00:00:00 |
|   10004 |     20007 | 2005-09-30 00:00:00 |
+---------+-----------+---------------------+
```
> 1. 使用between 起始日期 and 终止日期 这种格式来判断。
> 2. 这种方法不方便。

2. 方法2：
```
mysql> select cust_id,order_num,order_date from orders where year(order_date)='2005' and month(order_date)='09';
+---------+-----------+---------------------+
| cust_id | order_num | order_date          |
+---------+-----------+---------------------+
|   10001 |     20005 | 2005-09-01 00:00:00 |
|   10003 |     20006 | 2005-09-12 00:00:00 |
|   10004 |     20007 | 2005-09-30 00:00:00 |
+---------+-----------+---------------------+
```
> 1. 使用year()和month()函数来获取年和月份。

# 第12章 汇总数据
函数|说明
|:-:|:-:|
avg()|平均值
count()|列的数目
max()|当前列的最大值
min()|当前列的最小值
sum()|当前列的之和

* avg()函数
```
mysql> select avg(prod_price) as price_avg from products where vend_id = 1003;
+-----------+
| price_avg |
+-----------+
| 13.212857 |
+-----------+
```
> 1. 查询出vend_id = 1003的平均价格;
> 2. avg()函数为求平均的价格;
> 3. 值为null的行将会被忽略。

* count()函数
1. count(*)的使用
```
mysql> select count(*) as num_cust from customers;
+----------+
| num_cust |
+----------+
|        5 |
+----------+
```
> 1. 找出customers表中的所有行的数目;
> 2. count(*)将统计任意值的行，包括null值。

2. count(列名称)的使用
```
mysql> select count(cust_email) as num_cust from customers;
+----------+
| num_cust |
+----------+
|        3 |
+----------+
```
> 1. cust_email中有null值，所以显示的结果就为3。（总共是5行）

* max()函数
```
mysql> select max(prod_price) as max_price from products;
+-----------+
| max_price |
+-----------+
|     55.00 |
+-----------+
```
> 1. 找出最大值
> 2. 忽略null值。

* min()函数
```
mysql> select min(prod_price) as min_price from products;
+-----------+
| min_price |
+-----------+
|      2.50 |
+-----------+
```
> 1. 找出最小值，且会忽略null行。

* sum()函数
```
mysql> select sum(item_price * quantity) as total_price from orderitems where order_num = 20005;
+-------------+
| total_price |
+-------------+
|      149.87 |
+-------------+
```
> 1. 返回价格之和，且会忽略null的行。

* 用distinct聚合不同值
```
mysql> select avg(distinct prod_price) as avg_price from products where vend_id = 1003;
+-----------+
| avg_price |
+-----------+
| 15.998000 |
+-----------+
```
> 1. 使用distinct就是将相同的值只显示1个。

* 组合聚合函数
```
mysql> select count(*) as num_items,avg(prod_price) as avg_price,min(prod_price) as min_price,max(prod_price) as max_price from products;
+-----------+-----------+-----------+-----------+
| num_items | avg_price | min_price | max_price |
+-----------+-----------+-----------+-----------+
|        14 | 16.133571 |      2.50 |     55.00 |
+-----------+-----------+-----------+-----------+
```

# 第13章 分组数据(group by/having/order by的使用)

* group by创建分组
```
mysql> select vend_id,count(*) as num_prods from products group by vend_id;
+---------+-----------+
| vend_id | num_prods |
+---------+-----------+
|    1001 |         3 |
|    1002 |         2 |
|    1003 |         7 |
|    1005 |         2 |
+---------+-----------
```
> 1. 通过vend_id创建分组；
> 2. 如果当前列中的行有null值，会单独归为一行，多个null值归为一组。

* having分组过滤
```
mysql> select cust_id,count(*) as orders from orders group by cust_id having count(*)>=2;
+---------+--------+
| cust_id | orders |
+---------+--------+
|   10001 |      2 |
+---------+--------+
```
> 1. 找出订单数大于等于2的顾客。
> 2. where是在数据分组前进行过滤，having是在数据分组之后进行过滤。

* where与having的共同使用
```
mysql> mysql> select vend_id,count(*) as num_prod from products where prod_price >= 10 group by vend_id having count(*) >= 2;
+---------+-----------------+
| vend_id | order_nnum_prod |
+---------+-----------------+
|    1003 |               4 |
|    1005 |               2 |
+---------+-----------------+
```
> 1. where是过滤掉价格小于10的数据，通过group by分组，having过滤掉小于2的数据。

* 分组和order by排序
```
mysql> select order_num,sum(quantity*item_price) as ordertotal from orderitems group by order_num having sum(quantity*item_price) >= 50 order by ordertotal;
+-----------+------------+
| order_num | ordertotal |
+-----------+------------+
|     20006 |      55.00 |
|     20008 |     125.00 |
|     20005 |     149.87 |
|     20007 |    1000.00 |
+-----------+------------+
```
> 1. 最后通过group by将分组后的数据进行排序。

