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
    -> order_num int not null,
    -> order_item int not null,
    -> prod_id char(10) not null,
    -> quantity int not null,
    -> item_price decimal(8,2) not null,
    -> primary key (order_num,order_item)
    -> )engine=InnoDB;
```
> 注意部分：
> 1. 多个列组成一个主键的格式，用逗号隔开；
> 2. 设置默认值，比如这个表格中的quantity列设置：
>   ```quantity int not null default 1```
>   这个就是设置默认值为1。

* 创建product表
```
create table produces(
    -> prod_id char(10) not null,
    -> vend_id int not null,
    -> prod_name char(255) not null,
    -> prod_price decimal(8,2) not null,
    -> prod_desc text null,
    -> primary key (prod_id)
    -> )engine=InnoDB;
```
> 注意部分：
> 1. text数据类型：最大长度为64K的变长文本。