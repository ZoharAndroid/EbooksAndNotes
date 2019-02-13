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

