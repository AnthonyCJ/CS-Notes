# 

---



# 目录





[TOC]









---

# 1. 了解SQL

综述：介绍数据库和SQL。





##  1.1 数据库基础



### 1.1.1 什么是数据库

* **数据库（database）**：保存有组织的数据的容器。（通常是一个文件或一组文件）

> 数据库可以理解为**以一个有组织的方式存储**的**数据集合**。可以将**数据库**想象为一个**文件柜**，此文件柜是一个存放数据的物理位置，不管数据是什么以及是如何组织的。
>
> * 概念区分：数据库与数据库软件
>   * 数据库是一个保存数据的容器
>   * 数据库软件（DBMS：Database Management System）是数据库管理系统。
>   * **使用者**不直接访问数据库，而是**通过DBMS**访问和**操作数据库**。



### 1.1.2 表

* **表（table）**：某种特定类型数据的结构化清单。
  * 注意：存储在表中的数据是**一种类型的数据**或**一个清单**。绝不可以将顾客的清单与订单的清单存储在同一个数据库表中。这样会导致检索和访问操作困难。
  * **表名**：表名的唯一性取决于多个因素，如数据库名和表明的结合。
    * 同一数据库表名不可重复，不同数据库表名可重复。
  * **模式（schema）**：关于数据库和表的布局及特性的信息。



### 1.1.3 列和数据类型

表由列组成。列中存储表的某部分信息。

* **列（column）**：表中的一个**字段**。所有的表都是由一个或多个列组成的。
* **数据类型（datatype）**：所容许的数据的类型。每个表都有相应的数据类型。它限制（或容许）该列中存储的数据。

> 数据类型限制可存储在列中的数据种类。【例如，防止在数值字段录入字符值】。数据类型还帮助正确地排序数据，并在优化磁盘使用方面起重要作用。
>
> 建表时应对数据类型给予特别关注。



### 1.1.4 行

* **行（row）**：表中的一个记录。

> 很多时候，行（row）和记录（record）两个数据可以相互替代，但从技术上说，行是正确的术语。



### 1.1.5 主键

* **主键（primary key）**：一列（或一组列），其值能够**唯一区分**表中的**每个行**。
  * 作为主键的条件
    1. 任意两行都不具有相同的主键值
    2. 每个行都必须具有一个主键值（主键列**不允许NULL**值）

主键通常定义在表的一列上，但不是必需的。也可以使用多个列作为主键。使用多列作为主键时，上述条件必须应用到构成主键的所有列，所有列值的组合必须唯一（但单个列的值可以不唯一）。

由于主键的作用十分重要，如何选取主键会对业务开发产生重要影响。如果我们以学生的身份证号作为主键，似乎能唯一定位记录。然而，身份证号也是一种业务场景，如果身份证号升位了，或者需要变更，作为主键，不得不修改的时候，就会对业务产生严重影响。

所以，选取主键的一个基本原则是：**不使用任何业务相关的字段作为主键**。

因此，身份证号、手机号、邮箱地址这些看上去可以唯一的字段，均**不可**用作主键。

作为主键最好是完全业务无关的字段，我们一般把这个字段命名为`id`。常见的可作为`id`字段的类型有：

1. **自增整数类型**：数据库会在插入数据时自动为每一条记录分配一个自增整数，这样我们就完全不用担心主键重复，也不用自己预先生成主键；
2. **全局唯一GUID类型**：使用一种全局唯一的字符串作为主键，类似`8f55d96b-8acc-4636-8cb8-76bf8abc2f57`。GUID算法通过网卡MAC地址、时间戳和随机数保证任意计算机在任意时间生成的字符串都是不同的，大部分编程语言都内置了GUID算法，可以自己预算出主键。

对于大部分应用来说，通常自增类型的主键就能满足需求。我们在`students`表中定义的主键也是`BIGINT NOT NULL AUTO_INCREMENT`类型。

> 注：如果使用INT自增类型，那么当一张表的记录数超过2147483647（约21亿）时，会达到上限而出错。使用BIGINT自增类型则可以最多约922亿亿条记录。

> 主键的好习惯：
>
> * 不更新主键列中的值
> * 不重用主键列的值
> * 不在主键列中使用可能会该的值。



### 1.1.6 外键

* 参考连接：[外键 - 廖雪峰](https://www.liaoxuefeng.com/wiki/1177760294764384/1218728424164736)

当我们用主键唯一标识记录时，我们就可以在`students`表中确定任意一个学生的记录：

| id   | name | other columns... |
| ---- | ---- | ---------------- |
| 1    | 小明 | ...              |
| 2    | 小红 | ...              |

我们还可以在`classes`表中确定任意一个班级记录：

| id   | name | other columns... |
| ---- | ---- | ---------------- |
| 1    | 一班 | ...              |
| 2    | 二班 | ...              |

但是我们如何确定`students`表的一条记录，例如，`id=1`的小明，属于哪个班级呢？

由于一个班级可以有多个学生，在关系模型中，这两个表的关系可以称为“一对多”，即一个`classes`的记录可以对应多个`students`表的记录。

为了表达这种一对多的关系，我们需要在`students`表中加入一列`class_id`，让它的值与`classes`表的某条记录相对应：

| id   | class_id | name | other columns... |
| ---- | -------- | ---- | ---------------- |
| 1    | 1        | 小明 | ...              |
| 2    | 1        | 小红 | ...              |
| 5    | 2        | 小白 | ...              |

这样，我们就可以根据`class_id`这个列直接定位出一个`students`表的记录应该对应到`classes`的哪条记录。

例如：

- 小明的`class_id`是`1`，因此，对应的`classes`表的记录是`id=1`的一班；
- 小红的`class_id`是`1`，因此，对应的`classes`表的记录是`id=1`的一班；
- 小白的`class_id`是`2`，因此，对应的`classes`表的记录是`id=2`的二班。

在`students`表中，通过`class_id`的字段，可以把数据与另一张表关联起来，这种列称为`外键`。

外键并不是通过列名实现的，而是通过**定义外键约束**实现的：

~~~mysql
ALTER TABLE students
ADD CONSTRAINT fk_class_id
FOREIGN KEY (class_id)
REFERENCES classes (id);
~~~

其中，外键约束的名称`fk_class_id`可以任意，`FOREIGN KEY (class_id)`指定了`class_id`作为外键，`REFERENCES classes (id)`指定了这个外键将关联到`classes`表的`id`列（即`classes`表的主键）。

通过定义外键约束，关系数据库可以保证无法插入无效的数据。即如果`classes`表不存在`id=99`的记录，`students`表就无法插入`class_id=99`的记录。

由于外键约束会降低数据库的性能，大部分互联网应用程序为了追求速度，并不设置外键约束，而是仅靠应用程序自身来保证逻辑的正确性。这种情况下，`class_id`仅仅是一个普通的列，只是它起到了外键的作用而已。

要**删除外键约束**，也是通过`ALTER TABLE`实现的：

~~~mysql
ALTER TABLE students
DROP FOREIGN KEY fk_class_id;
~~~

注意：

* 删除外键约束并没有删除外键这一列。删除列是通过`DROP COLUMN ...`实现的。
* **删除外键约束**命令使用的是**外键名**而不是字段名。



### 1.1.7 索引

* 参考连接：[索引 - 廖雪峰](https://www.liaoxuefeng.com/wiki/1177760294764384/1218728442198976)





## 1.2 什么是SQL



### 1.2.1 相关概念

* **SQL（Structured Query Language）**：是一种专门用来与数据库通信的语言。

设计SQL的目的是提供一种从数据库中读写数据的简单有效的方法。

SQL是结构化查询语言的缩写，用来访问和操作数据库系统。SQL语句既可以查询数据库中的数据，也可以添加、更新和删除数据库中的数据，还可以对数据库进行管理和维护操作。不同的数据库，都支持SQL，这样，我们通过学习SQL这一种语言，就可以操作各种不同的数据库。

SQL语言定义了这么几种操作数据库的能力：

* **DDL：Data Definition Language**
  * DDL允许用户定义数据，也就是创建表、删除表、修改表结构这些操作。通常，DDL由数据库管理员执行。
* **DML：Data Manipulation Language**
  * DML为用户提供添加、删除、更新数据的能力，这些是应用程序对数据库的日常操作。
* **DQL：Data Query Language**
  * DQL允许用户查询数据，这也是通常最频繁的数据库日常操作。

### 1.2.2 语法特点

SQL语言关键字不区分大小写！！！但是，针对不同的数据库，对于表名和列名，有的数据库区分大小写，有的数据库不区分大小写。同一个数据库，有的在Linux上区分大小写，有的在Windows上不区分大小写。

* 个人习惯遵循的约定：==**SQL关键字**==总是==**大写**==，以示突出，==**表名和列名**==均使用==**小写**==。





## 1.3 小结

* 介绍了SQL的一些基本术语和概念。

* 主键
  * 主键是关系表中记录的唯一标识。主键的选取非常重要：主键不要带有业务含义，而应该使用BIGINT自增或者GUID类型。主键也不应该允许`NULL`。
  * 可以使用多个列作为联合主键，但联合主键并不常用。
* 外键
  * 关系数据库通过外键可以实现一对多、多对多和一对一的关系。外键既可以通过数据库来约束，也可以不设置约束，仅依靠应用程序的逻辑来保证。
* 索引
  * 通过对数据库表创建索引，可以提高查询速度。
  * 通过创建唯一索引，可以保证某一列的值具有唯一性。
  * 数据库索引对于用户和应用程序来说都是透明的。









---

# 2. MySQL简介

综述：介绍什么是MySQL，以及MySQL中可以应用什么工具。





## 2.1 什么是MySQL

* MySQL是一种DBMS，即它是一种数据库软件。



### 2.1.1 客户机-服务器软件

MySQL、Oracle、Microsoft SQL Server 等数据库都是基于客户机-服务器的数据库。kehuji-fuwuqi应用分为两个不同的部分。服务器部分是负责所有数据访问和处理的一个软件。这个软件运行在称为数据库服务器的计算机上。

与数据文件打交道的只有**服务器软件**。关于数据、数据添加、删除和数据更新的所有请求都由服务器软件完成。**客户机软件**通过网络提交请求给服务器软件。服务器软件处理这个请求，根据需要过滤、丢弃和排序数据，然后将结果响应给客户机软件。

> 客户机和服务器软件可能安装在两台计算机或一台计算机上。为了进行数据库交互，客户机软件要与服务器软件进行通信。

所有这些活动对用户是透明的。事实上，多数网络的建立使用户不具有对数据的访问权，甚至不具有对存储数据的驱动器的访问权。

这样，为了使用MySQL，需要**访问运行MySQL服务器软件的计算机**【客户机】和**发布命令到MySQL客户机软件的计算机**【服务器】。

* 服务器软件为MySQL DBMS。你可以在本地安装的副本上运行，也可以连接到运行在你具有访问权限的远程服务器上的一个副本。
* 客户机可以是MySQL提供的工具、脚本语言（如Perl）、Web应用开发语言（如ASP、ColdFusion、JSP和PHP）、程序设计语言（如C、C++、Java）等。



### 2.1.2 MySQL版本

系列版本中引入的一些更改。

* 4——InnoDB引擎，增加**事务处理**、**并**、**改进全文本搜索**等的支持。
* 4.1——对函数库、子查询、继承帮助等的重要增加。
* 5——存储过程、触发器、游标、视图等。





## 2.2 MySQL工具



### 2.2.1 mysql命令行实用程序

* 命令输入在 mysql> 之后
* 命令用 ；或 \g 结束。仅按 Enter 不执行命令
* 输入quit或exit推出命令行实用程序



### 2.2.2 MySQL Administrator



### 2.2.3 MySQL Query Brower





## 2.3 小结

* 介绍了什么是MySQL。
* 引入了几个客户机实用程序。









---

#3. 使用MySQL

本节内容为如何连接和登录到MySQL，如何执行MySQL语句，以及如何获得数据库和表的信息。





## 3.1 连接

MySQL与所有客户机-服务器DBMS一样，要求在能之心工龄之前登录到DBMS。登录名可以与网络登录名不相同。MySQL在内部保存自己的用户列表，并且把每个用户与各种权限关联起来。

* 为了连接到MySQL，需要以下信息
  * 主机名（计算机名）——如果连接到本地MySQL服务器，为 **localhost**
  * 端口（如果使用默认端口 3306 之外的端口）
  * 一个合法的用户名
  * 用户口令（如果需要）



### MySQL数据库软件基本操作

1. 安装
2. 卸载
   1. 在mysql的安装目录找到my.ini文件
      * 复制 datadir=D:\SourceFiles\mysql\mysql-8.0.25-winx64\data
   2. 卸载MySQL
   3. 删除C:/ProgramData目录下的MySQL文件夹。
3. 配置
   * **MySQL服务的启动**
     1. 手动
     2. cmd --> services.msc  // 打开服务的窗口
     3. 使用管理员打开cmd
        * net start mysql：启动mysql的服务
        * net stop mysql：关闭mysql服务
   * **MySQL登录**
     1. mysql -uroot -p + 输入密码 【默认连接本地 MySQL】
     2. mysql -hip -uroot -p连接目标的密码【本机ip为：127.0.0.1】
     3. mysql --host=ip --user=root --password=连接目标的密码
   * **MySQL退出**
     1. exit
     2. quit

   * MySQL目录结构

     1.  MySQL安装目录：basedir="D:\SourceFiles\mysql\mysql-8.0.25-winx64\"

        * bin：二进制可执行文件
        * data：数据目录
        * include：存放c语言的一些头文件
        * lib：库文件
        * share：MySQL的错误信息

        * ==**配置文件**== my.ini
     
     2. MySQL数据目录：datadir=D:\SourceFiles\mysql\mysql-8.0.25-winx64\data
     
        * 几个概念
          * 数据库：文件夹
          * 表：文件
          * 数据：数据





## 3.2 选择数据库

在执行任意数据库操作前，需要使用 **USE** 关键字选择一个数据库。

* **关键字（key word）**：作为MySQL语言组成部分的一个保留字。决不要用关键字命名一个表或列。

### 输入

~~~mysql
USE crashcourse;	-- 选择数据库crashcourse
~~~

### 输出

~~~powershell
Database changed
~~~

### 分析

USE 语句并不返回任何结果。依赖于使用的客户机，显示某种形式的通知。例如，这里显示出的 Database changed 消息是 mysql 命令行实用程序在数据库选择成功后显示的。





## 3.3 查看数据库和表



### 3.3.1查看所有数据库

#### 输入

~~~mysql
SHOW DATABASES;	-- 返回可用数据库的一个列表
~~~

#### 输出

~~~bash
+--------------------+
| Database           |
+--------------------+
| crashcourse        |
| day14              |
| db1                |
| db2                |
| db3                |
| db4                |
| dbtest             |
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
11 rows in set (0.00 sec)
~~~

#### 分析

**SHOW DATABASES;** 返回可用数据库的一个列表。列表中的数据库可能是 MySQL 内部使用的数据库（如例子中的 mysql 和 information_schema），也可能是用户自己创建的数据库。



### 3.3.2 查看所有表

为了获得一个数据库内的表的列表，使用如下命令。

#### 输入

~~~mysql
SHOW TABLES;	-- 查看所有表
~~~

#### 输出

~~~bash
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
~~~

#### 分析

**SHOW TABLES;** 返回当前选择的数据库内可用表的列表。



### 3.3.3 显示某表的所有列

#### 输入

~~~mysql
SHOW COLUMNS FROM customers;	-- 显示 customers 表的所有列
~~~

#### 输出

~~~bash
+--------------+-----------+------+-----+---------+----------------+
| Field        | Type      | Null | Key | Default | Extra          |
+--------------+-----------+------+-----+---------+----------------+
| cust_id      | int       | NO   | PRI | NULL    | auto_increment |
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
~~~

#### 分析

**SHOW COLUMNS;** 要求给出一个表名，它对每个字段返回一行，行中包含**字段名**、**数据类型**、**是否允许NULL**、**键信息**、**默认值**以及**其它信息**（如字段 cust_id 的 auto_increment）。





## 3.4 小结

* 连接、登录MySQL
* 如何用 USE 选择数据库
* 如何用 SHOW 查看MySQL数据库、表和内部信息。









---

# 4. 检索数据

本节内容为使用SELECT语句从表中检索一个或多个数据列。





## 4.1 SELECT 语句

用途：从一个或多个表中检索信息

为了使用 SELECT 检索表数据，必须至少给出两条信息——想选择什么，以及从什么地方选择。

~~~mysql
SELECT "what" FROM "where"
~~~





## 4.2 检索单个列

### 输入

~~~mysql
SELECT prod_name
FROM products;
~~~

### 分析

上述语句利用 SELECT 语句从 products 表中检索一个名为 prod_name 的列。所需的列明在 SELECT 关键字之后给出；FROM 关键字之处目标检索数据所在的表的表名。

### 输出

~~~bash
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
14 rows in set (0.00 sec)
~~~

### 附：SQL语句大小写问题

> SQL语句不区分大小写。推荐使用固定的规范：SQL==**关键字**==统一==**大写**==，所有==**列名、表名小写**==。
>
> 标识符区是分大小写的。
>
> 推荐**大小写**规范**应明确、固定、一致**。





## 4.3 检索多个列

从一个表中检索多个列，必须在 SELECT 关键字后给出多个列名，==**列名之间**==必须==**以逗号分隔**==。

### 输入

~~~mysql
SELECT prod_id, prod_name, prod_price
FROM products;
~~~

### 输出

~~~bash
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
14 rows in set (0.00 sec)
~~~

### 附：数据表示

> SQL语句一般返回原始的、无格式的数据。**数据的格式化是一个表示问题，而不是一个检索问题**。因此，**表示**（对齐和显示上面的价格值，用货币符号和逗号表示其金额）**一般在显示该数据的应用程序中规定**。一般很少使用实际检索出的原始数据（没有应用程序提供的格式）。





## 4.4 检索所有列

### 输入

~~~mysql
SELECT * FROM products;
~~~

### 输出

~~~bash
+---------+---------+----------------+------------+----------------------------------------------------------------+
| prod_id | vend_id | prod_name      | prod_price | prod_desc                                                      |
+---------+---------+----------------+------------+----------------------------------------------------------------+
| ANV01   |    1001 | .5 ton anvil   |       5.99 | .5 ton anvil, black, complete with handy hook                  |
| ANV02   |    1001 | 1 ton anvil    |       9.99 | 1 ton anvil, black, complete with handy hook and carrying case |
| ANV03   |    1001 | 2 ton anvil    |      14.99 | 2 ton anvil, black, complete with handy hook and carrying case |
| DTNTR   |    1003 | Detonator      |      13.00 | Detonator (plunger powered), fuses not included                |
| FB      |    1003 | Bird seed      |      10.00 | Large bag (suitable for road runners)                          |
| FC      |    1003 | Carrots        |       2.50 | Carrots (rabbit hunting season only)                           |
| FU1     |    1002 | Fuses          |       3.42 | 1 dozen, extra long                                            |
| JP1000  |    1005 | JetPack 1000   |      35.00 | JetPack 1000, intended for single use                          |
| JP2000  |    1005 | JetPack 2000   |      55.00 | JetPack 2000, multi-use                                        |
| OL1     |    1002 | Oil can        |       8.99 | Oil can, red                                                   |
| SAFE    |    1003 | Safe           |      50.00 | Safe with combination lock                                     |
| SLING   |    1003 | Sling          |       4.49 | Sling, one size fits all                                       |
| TNT1    |    1003 | TNT (1 stick)  |       2.50 | TNT, red, single stick                                         |
| TNT2    |    1003 | TNT (5 sticks) |      10.00 | TNT, red, pack of 10 sticks                                    |
+---------+---------+----------------+------------+----------------------------------------------------------------+
14 rows in set (0.00 sec)
~~~





## 4.5 检索不同的行

### 目标

检索表 PRODUCTS 中产品的所有供应商ID （vend_id），要求检索的同时去掉重复的的id

### 输入

~~~mysql
SELECT DISTINCT vend_id
FROM products;
~~~

### 分析

DISTINCT 关键字必须直接放在列名的前面。SELECT DISTINCT vend_id 告诉MySQL只返回不同的（唯一的）vend_id 行，因此只返回4行。

> **不能部分使用 DISTINCT 关键字**
>
> **DISTINCT** 关键字==**应用于所有列**==而不仅是前置它的列，如果给出
>
> ~~~mysql
> SELECT DISTINC vend_id, prod_price FROM products;
> ~~~
>
> 除非指定的两个列都相同，否则所有行都将被检索出来。

### 输出

~~~bash
+---------+
| vend_id |
+---------+
|    1001 |
|    1002 |
|    1003 |
|    1005 |
+---------+
4 rows in set (0.00 sec)
~~~





## 4.6 限制结果

可用 LIMIT 子句限制查询返回的行数。

### 输入

~~~mysql
SELECT prod_name
FROM products
LIMIT 5;
~~~

### 分析

此句用 SELECT 关键字检索单个列。LIMIT 5 表示MySQL返回不超过5行【这里得出的是查询结果的前5行】。

### 输出

~~~bash
+--------------+
| prod_name    |
+--------------+
| .5 ton anvil |
| 1 ton anvil  |
| 2 ton anvil  |
| Detonator    |
| Bird seed    |
+--------------+
5 rows in set (0.00 sec)
~~~

如果想得到指定部分的查询结果，可以**指定返回的起始位置**。

### 输入

~~~mysql
SELECT prod_name
FROM products
LIMIT 5, 5;
~~~

### 分析

LIMIT 5, 5 表示MySQL返回从第5行开始的5行结果。第1个数为开始位置，第2个数为要检索的行数。

> **行0** ： **行的索引**是**从0开始**的。因此，语句
>
> ~~~mysql
> LIMIT 1, 1
> ~~~
>
> 将检索实际的第2行而不是第1行。
>
> **行数小于限制**时，将仅仅返回符合要求的所有行。

### 输出

~~~BASH
+--------------+
| prod_name    |
+--------------+
| Carrots      |
| Fuses        |
| JetPack 1000 |
| JetPack 2000 |
| Oil can      |
+--------------+
5 rows in set (0.00 sec)
~~~

### 附：MySQL5 的新LIMIT语法

~~~mysql
LIMIT 4 OFFSET 3	-- 表示从行3开始取4行
~~~





## 4.7 使用完全限定的表名

数据库名.表名

表名.列名





## 4.8 小结

* 学使用 SQL 的 SELECT 语句**检索**单个表列、多个表列、所有表列。
* 下节将学习**排序**检索的数据。









---



# 5. 排序检索数据

- 使用 SELECT 语句的 ORDER BY 子句，根据需要排序检索的数据。





## 5.1 排序数据

* **子句（clause）**：SQL语句由子句构成，有些必需，有些可选。一个子句通常由一个关键字和所提供的数据组成。

ORDER BY 子句取一个或多个列的名字，据此对输出进行排序。

### 输入

~~~mysql
SELECT prod_name
FROM products
ORDER BY prod_name;
~~~

### 分析

此句指示MySQL对 prod_name 查询结果列以 prod_name 列**字母顺序**排序数据。

### 输出

~~~bash
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
14 rows in set (0.00 sec)
~~~





## 5.2 按多个列排序

**注意**：按多个列排序时，排序按照所规定的顺序进行。仅当第一个排序条件无法判断先后时才会按第二个排序条件排序，以此类推。

###  输入

~~~mysql
SELECT prod_id, prod_price, prod_name 
FROM products 
ORDER BY prod_price, prod_name;
~~~

### 输出

~~~bash
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
14 rows in set (0.00 sec)
~~~





## 5.3 指定排序方向

MySQL**默认按升序**排列。如果想要按==**降序**==排列，需要在字段后加 ==**DESC**== 关键字。

举例如下：按价格降序排列产品。

### 输入

~~~mysql
SELECT prod_id, prod_price, prod_name 
FROM products 
ORDER BY prod_price DESC, prod_name;	-- 按价格降序排列
~~~

### 输出

~~~bash
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
14 rows in set (0.00 sec)
~~~

* 若要对多个列同时进行降序限定，则要对每个字段分别加上关键字 DESC

~~~mysql
SELECT prod_id, prod_price, prod_name 
FROM products 
ORDER BY prod_price DESC, prod_name;	-- 按价格降序排列
~~~

* 与 DESC 关键字相反的是 ASC（ASCENDING），实际上可以不用指定，因为升序是默认的。

使用 ORDER BY 和 LIMIT 组合，可以找出一个列中的最大值或最小值。

* 例：查找  products 表中最贵物品的值

### 输入

~~~mysql
SELECT prod_price
FROM products 
ORDER BY prod_price DESC
LIMIT 1;	-- 查找降序第1个值【即最大值】
~~~

### 输出

~~~bash
+------------+
| prod_price |
+------------+
|      55.00 |
+------------+
1 row in set (0.01 sec)
~~~





## 5.4 ORDER BY 等关键字在语句中的位置

> **ORDER BY 句子的位置**
>
> 几个子句【如果都有】的合法顺序为
>
> **SELECT	columns	FROM	table	ORDER BY	columns	LIMIT	rows	**



## 5.5 小结

* 用 SELECT 语句的 ORDER BY 子句对检索出的数据进行排序。
* ==**这个子句必须是 SELECT 语句中的最后一条子句**==。
* 可根据需要，利用它在一个或多个列上对数据进行排序。









---

# 6. 过滤数据

* 使用 SELECT 语句的 **WHERE** 子句**指定搜索条件**。





## 6.1 使用 WHERE 子句

为什么要使用 WHERE 子句？

数据库表一般包含大量的数据，很少需要检索表中的所有行。**通常只会**根据特定操作或报告的需要**提取表数据的子集**。只检索所需数据需要指定**搜索条件（search criteria）**，搜索条件也成为**过滤条件（filter condition）**。

在 SELECT 语句中，数据根据 WHERE 子句中指定的搜索条件进行过滤。WHERE 子句在表名（FROM 子句）之后给出，如下所示：

### 输入

~~~MYSQL
SELECT prod_name, prod_price
FROM products
WHERE prod_price = 2.50;
~~~

### 分析

该语句从 products 表中检索两个列，返回 prod_price 值为 2.50 的行。

### 输出

~~~bash
+---------------+------------+
| prod_name     | prod_price |
+---------------+------------+
| Carrots       |       2.50 |
| TNT (1 stick) |       2.50 |
+---------------+------------+
2 rows in set (0.01 sec)
~~~

### WHERE 子句的位置

在同时使用 ORDER BY 和 WHERE 子句时，应该让 ORDER BY 位于 WHERE 之后，否则将会产生错误。





## 6.2 WHERE 子句操作符

下标列出 MySQL 支持的所有条件操作符。

| 操作符  | 说明               |
| ------- | ------------------ |
| =       | 等于               |
| <>      | 不等于             |
| !=      | 不等于             |
| <       | 小于               |
| <=      | 小于等于           |
| >       | 大于               |
| >=      | 大于等于           |
| BETWEEN | 在指定的两个值之间 |



### 6.2.1 检查单个值【=】

**例1**：测试相等

#### 输入

~~~MYSQL
SELECT prod_name, prod_price 
FROM products 
WHERE prod_name = 'fuses';
~~~

#### 输出

~~~bash
+-----------+------------+
| prod_name | prod_price |
+-----------+------------+
| Fuses     |       3.42 |
+-----------+------------+
1 row in set (0.00 sec)
~~~

#### 

**例2**：列出价格小于10美元的所有产品【的产品名和价格】

#### 输入

~~~mysql
SELECT prod_name, prod_price 
FROM products 
WHERE prod_price < 10;
~~~

#### 输出

~~~bash
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
7 rows in set (0.00 sec)
~~~



### 6.2.2 不匹配检查【!=】

**例1**：列出不是由供应商1003制造的所有产品【的供应商id和产品名】

#### 输入

~~~mysql
SELECT vend_id, prod_name
FROM products 
WHERE vend_id <> 1003;	-- 等价子句 WHERE vend_id != 1003
~~~

#### 输出

~~~bash
+---------+--------------+
| vend_id | prod_name    |
+---------+--------------+
|    1001 | .5 ton anvil |
|    1001 | 1 ton anvil  |
|    1001 | 2 ton anvil  |
|    1002 | Fuses        |
|    1002 | Oil can      |
|    1005 | JetPack 1000 |
|    1005 | JetPack 2000 |
+---------+--------------+
7 rows in set (0.01 sec)
~~~

#### 附：何时使用引号

> 单引号用来限定字符串。
>
> 如果**将值与串类型的列进行比较**，则**需要限定引号**。用来与数值列进行比较的值不用引号。



### 6.2.3 范围值检查【BETWEEN AND】

例：使用 BETWEEN 操作符，检索价格在5美元和10美元之间的产品【返回产品名和产品价格】

#### 输入

~~~mysql
SELECT prod_name, prod_price
FROM products 
WHERE prod_price BETWEEN  5 AND 10;
~~~

#### 输出

~~~bash
+----------------+------------+
| prod_name      | prod_price |
+----------------+------------+
| .5 ton anvil   |       5.99 |
| 1 ton anvil    |       9.99 |
| Bird seed      |      10.00 |
| Oil can        |       8.99 |
| TNT (5 sticks) |      10.00 |
+----------------+------------+
5 rows in set (0.01 sec)
~~~

#### 分析

使用 BETWEEN 关键字时，必须指定所需范围的**低端值**和**高端值**，必须**用 AND 分隔**。

**BETWEEN AND 匹配范围**中**包括**指定的**开始值**和**结束值**。



### 6.2.4 空值检查【IS NULL】

建表时，表设计人员可以指定其中的列是否可以不包含值。在一个列不包含值时，称其为包含空值 NULL。

* **NULL	无值（no value）**：它与字段包含0、空字符串或仅仅包含空格不同。

例：通过 IS NULL 子句检查具有 NULL 值的列

#### 输入

~~~mysql
SELECT  cust_id
FROM customers
WHERE cust_email IS NULL;
~~~

#### 输出

~~~BASH
+---------+
| cust_id |
+---------+
|   10002 |
|   10005 |
+---------+
2 rows in set (0.00 sec)
~~~





## 6.3 小结

* 用 SELECT 语句的 WHERE 子句 过滤返回的数据。
* 相等、不等、大于、小于、范围区间。
* NULL 值。









---



# 7. 数据过滤

* 使用**组合 WHERE 子句**以建立功能更强的更高级的搜索条件。
*  **NOT** 和 **IN** 操作符。





## 7.1 组合 WHERE 子句

MySQL允许给出**多个 WHERE 子句**。这些子句可以**两种**方式使用：**以 AND 子句的方式**或 **OR 子句的方式**使用。

* **操作符（operator）**：用来联结或改变 WHERE 子句中的子句的**关键字**。也成为**逻辑操作符（logical operator）**。

### 7.1.1 AND 操作符

* AND：用在 WHERE 子句中的关键字，用来指示检索满足所有给定条件的行。

> 可以添加多个过滤条件，没添加一条就要使用一个 AND。

为了通过不止一个列进行过滤，可以使用 AND 操作符给 WHERE 子句附加条件。举例如下：

#### 输入

~~~mysql
SELECT prod_id, prod_price, prod_name 
FROM products
WHERE vend_id = 1003 AND prod_price <= 10;
~~~

#### 分析

该语句检索【有供应商1003制造】**且**【价格小于等于10美元】的产品id、价格和名称。这条 SELECT 语句中的 WHERE 子句包含两个条件，且用 AND 关键字联结它们。AND 指示 DBMS 返回**满足所有给定条件**的行。

#### 输出

~~~bash
+---------+------------+----------------+
| prod_id | prod_price | prod_name      |
+---------+------------+----------------+
| FB      |      10.00 | Bird seed      |
| FC      |       2.50 | Carrots        |
| SLING   |       4.49 | Sling          |
| TNT1    |       2.50 | TNT (1 stick)  |
| TNT2    |      10.00 | TNT (5 sticks) |
+---------+------------+----------------+
5 rows in set (0.00 sec)
~~~



### 7.1.2 OR 操作符

OR 操作符指示 MySQL 检索匹配任一条件的行。示例如下：

#### 输入

~~~mysql
SELECT prod_name, prod_price 
FROM products
WHERE vend_id = 1002 OR vend_id = 1003;
~~~

#### 输出

~~~BASH
+----------------+------------+
| prod_name      | prod_price |
+----------------+------------+
| Fuses          |       3.42 |
| Oil can        |       8.99 |
| Detonator      |      13.00 |
| Bird seed      |      10.00 |
| Carrots        |       2.50 |
| Safe           |      50.00 |
| Sling          |       4.49 |
| TNT (1 stick)  |       2.50 |
| TNT (5 sticks) |      10.00 |
+----------------+------------+
9 rows in set (0.00 sec)
~~~



### 7.1.3 计算次序

* 注意：SQL 中，==**AND 操作符的优先级高于 OR 操作符**==。所以涉及业务逻辑时，推荐使用（）保证业务逻辑正确，**尽量避免受运算优先级影响**。





## 7.2 IN 操作符

* **IN**：WHERE 子句中用来指定要匹配值的清单的关键字，功能与 **OR** 相当。

IN 操作符用来指定条件范围，范围中的每个条件都可以进行匹配。IN 取和法值清单由圆括号包裹，逗号分隔。示例如下：

### 输入

~~~mysql
SELECT prod_name, prod_price 
FROM products 
WHERE vend_id IN (1002, 1003)
ORDER BY prod_name;
~~~

### 输出

~~~bash
+----------------+------------+
| prod_name      | prod_price |
+----------------+------------+
| Bird seed      |      10.00 |
| Carrots        |       2.50 |
| Detonator      |      13.00 |
| Fuses          |       3.42 |
| Oil can        |       8.99 |
| Safe           |      50.00 |
| Sling          |       4.49 |
| TNT (1 stick)  |       2.50 |
| TNT (5 sticks) |      10.00 |
+----------------+------------+
9 rows in set (0.01 sec)
~~~

### 分析

IN 操作符与 OR 关键字实现几乎相同的功能。

* 使用 IN 操作符有以下几条优点：
  * 在使用长的合法选项清单时，IN操作符的**语法更清楚**且**更直观**。
  * 使用IN时，计算的次序更容易管理（因为使用的操作符更少）。
  * IN操作符一般比OR操作符清单执行更快。
  * IN的最大优点是可以包含其它 SELECT 语句，使得能够更动态地建立 WHERE 子句。





## 7.3 NOT 操作符

* **NOT**：WHERE 子句中用来否定后跟条件的关键字。

WHERE 子句的 NOT 操作符有且仅有一个功能——否定它之后所跟的任何条件。

例：列出除了【vend_id为】1002和1003之外的所有供应商制造的产品。

### 输入

~~~mysql
SELECT prod_name, prod_price 
FROM products 
WHERE vend_id NOT IN (1002, 1003) 
ORDER BY prod_name;
~~~

### 输出

~~~bash
+--------------+------------+
| prod_name    | prod_price |
+--------------+------------+
| .5 ton anvil |       5.99 |
| 1 ton anvil  |       9.99 |
| 2 ton anvil  |      14.99 |
| JetPack 1000 |      35.00 |
| JetPack 2000 |      55.00 |
+--------------+------------+
5 rows in set (0.00 sec)
~~~

### 分析

为什么使用NOT？对于简单的 WHERE 子句，使用 NOT 确实么有什么优势，但在复杂的子句中，NOT 非常有用。

例如：在与 IN 操作符联合使用时，NOT 使找出与条件列表不匹配的行非常简单。

> 个人理解：NOT 可以看成是一个集合运算中的**取补集**或**取反**的操作。
>
> **MySQL 中的 NOT**
>
> **MySQL支持**使用 NOT 对 **IN**、**BETWEEN** 和 **EXISTS** 子句取反，这与多数其他DBMS允许使用 NOT 对各种条件取反有很大的差别。





## 7.4 小结

本节涉及

* 使用 AND 和 OR 操作符组合成 WHERE 子句，
* 如何明确的管理计算的次序【使用圆括号（）】
* 使用 IN 操作符
* 使用 NOT 操作符









---



# 8. 用通配符进行过滤

本节介绍什么是通配符、如何使用通配符以及怎样使用 LIKE 操作符进行通配搜索，以便对数据进行复杂过滤。

* **通配符（wildcard）**：用来**匹配值的一部分**的特殊字符。
* **搜索模式（search pattern）**：由字面值、通配符或两者组合构成的搜索条件。





## 8.1 LIKE 操作符

前面所介绍的操作符都是针对已知值进行过滤的。不管是匹配一个值还是多个值，测试大于还是小于已知值，或检查某个范围的值，共同点是**过滤中使用的值都是已知的**。但是，这种过滤方法并不在所有情况都适用。例如，如何搜索产品中**包含文本 anvil 的所有产品**？用简单的比较操作符是不行的，这里必须**使用通配符**。

利用通配符可以创建**比较特定数据的搜索模式**。在这个例子中，如果想找出名称包含 anvil 的所有产品，可构造一个通配符搜索模式，找出产品名中任何位置出现 anvil 的产品。可**构造一个通配符搜索模式**，找出产品名中任何位置出现 anvil 的产品。

通配符本身实际是 SQL 的 WHERE 子句中有特殊含义的字符，SQL 支持几种通配符。

**为了在搜索子句中使用通配符，必须使用 ==LIKE== 操作符**。LIKE 指示 MySQL，后跟的搜索模式利用通配符匹配而不是直接相等匹配进行比较。

> **谓词（predicate）**
>
> 操作符何时不是操作符？答案是它**作为谓词（predicate）时**。从技术上说，LIKE 是为此而不是操作符。虽然最终的结果是相同的，但应该对此术语有所了解。在 SQL 文档中会出现此术语。



### 8.1.1 百分号（%）通配符

最常使用的通配符即**百分号（%）**。在搜索串中，% **表示任何字符出现任意次数**。

**例1**：找出所有以词 jet 起头的产品。

#### 输入

~~~mysql
SELECT prod_id, prod_name 
FROM products 
WHERE prod_name LIKE 'jet%';
~~~

#### 输出

~~~bash
+---------+--------------+
| prod_id | prod_name    |
+---------+--------------+
| JP1000  | JetPack 1000 |
| JP2000  | JetPack 2000 |
+---------+--------------+
2 rows in set (0.00 sec)
~~~

#### 分析

此例子使用了**搜索模式 ‘jet%’**。在执行这条子句时，将检索任意以 jet 起头的词。%告诉MySQL接受 jet 之后的任意字符，不管有多少字符。

> **区分大小写**
>
> 注意：根据MySQL的配置方式，搜索可以是区分大小写的。区分大小写时，大小写不一致将不匹配。



**例2**：检索任何位置包含文本 anvil 的值

#### 输入

~~~mysql
SELECT prod_id, prod_name 
FROM products 
WHERE prod_name LIKE '%anvil%';
~~~

#### 输出

~~~bash
+---------+--------------+
| prod_id | prod_name    |
+---------+--------------+
| ANV01   | .5 ton anvil |
| ANV02   | 1 ton anvil  |
| ANV03   | 2 ton anvil  |
+---------+--------------+
3 rows in set (0.00 sec)
~~~

#### 分析

搜索模式 **'%anvil%'** 表示匹配任何位置包含文本 anvil 的值。



通配符也可以出现在搜索模式中间。

**例**：在好处以 s 开头以 e 结尾的所有产品

#### 输入

~~~mysql
SELECT prod_id, prod_name 
FROM products 
WHERE prod_name LIKE 's%e';
~~~

#### 输出

~~~bash
+---------+-----------+
| prod_id | prod_name |
+---------+-----------+
| SAFE    | Safe      |
+---------+-----------+
1 row in set (0.00 sec)
~~~

* **注意**：% 可以匹配 **0个**，1个或多个字符。

> **注意尾空格**
>
> 保存数据的尾空格可能会**干扰通配符匹配**。
>
> 解决办法：
>
> * 在搜索模式最后附加一个%。
> * 一个更好的办法是**使用函数**。

> **注意NULL**
>
> %通配符无法匹配 NULL 值。



### 8.1.2 下划线（_）通配符

下划线 _ 的用途与 % 一样，但只能匹配单个字符而不是多个字符。举例如下：

#### 输入

~~~mysql
SELECT prod_id, prod_name 
FROM products 
WHERE prod_name LIKE '_ ton anvil';
~~~

#### 输出

~~~bash
+---------+-------------+
| prod_id | prod_name   |
+---------+-------------+
| ANV02   | 1 ton anvil |
| ANV03   | 2 ton anvil |
+---------+-------------+
2 rows in set (0.00 sec)
~~~

#### 分析

与上方 % 检索结果对比更清晰。





## 8.2 使用通配符的技巧

MySQL的通配符很有用，但功能是有代价的——通配符搜索的处理一般要比前面讨论的其他搜索所画时间更长。这里给出一些使用通配符要记住的技巧：

* **不要过度使用通配符**。如果其他操作能达到相同的目的，应该使用其他操作符。
* 在确实需要使用通配符时，除非绝对有必要，否则**不要把通配符用在搜索模式的开始处**。把通配符置于搜素模式的开始处，搜索起来是最慢的。
* 仔细**注意通配符的位置**，如果放错地方，可能不会返回想要的数据。





## 8.3 小结

* 什么是通配符。
* 如何在 WHERE 子句中使用 SQL 通配符。
* 通配符应细心使用，不要过度使用。









---



# 9. 用正则表达式进行搜索

* 如何在MySQL  WHERE 子句内使用正则表达式来更好地控制数据过滤





## 9.1 正则表达式介绍

相较于前两节的匹配、比价、通配符操作寻找数据，正则表达式适用于更加复杂的条件。

正则表达式是用来匹配文本的特殊的串（字符集合）。

* 正则表达式的应用场景
  * 从一个文本文件中提取电话号码
  * 查找名字中间有数字的所有文件
  * 在一个文本块中找到所有重复的单词
  * 替换一个页面中的所有URL为这些URL的实际HTML链接

所有种类的程序设计语言、文本编辑器、操作系统等都支持正则表达式。

正则表达式用正则表达式语言建立，正则表达式语言是用来完成上述及更多工作的一种特殊语言，具有自身的语法和指令。





## 9.2 使用 MySQL 正则表达式

* 正则表达式与MySQL的关系：
  * **正则表达式的作用**是**匹配文本**，将一个模式（正则表达式）与一个文本串进行比较。
  * MySQL用 **WHERE 子句**对正则表达式提供了初步的支持，允许**指定正则表达式**，**过滤** SELECT 检索出的**数据**。

> 注：MySQL仅支持正则表达式语言的一个很小的子集。



### 9.2.1 基本字符匹配

从一个简单的例子开始。

**例1**：检索列 prod_name 包含文本 '1000' 的所有行

#### 输入

~~~mysql
SELECT prod_name 
FROM products 
WHERE prod_name REGEXP '1000' 
ORDER BY prod_name;
~~~

#### 输出

~~~bash
+--------------+
| prod_name    |
+--------------+
| JetPack 1000 |
+--------------+
1 row in set (0.01 sec)
~~~

#### 分析

从格式上看，仅仅是关键字 **LIKE** 被 **REGEXP** 替代。该语句告诉MySQL：**REGEXP** 后所跟的东西作为**正则表达式**（与文字正文1000匹配的一个正则表达式）处理。



**例2**：

#### 输入

~~~mysql
SELECT prod_name 
FROM products 
WHERE prod_name REGEXP '.000' 
ORDER BY prod_name;
~~~

#### 输出

~~~bash
+--------------+
| prod_name    |
+--------------+
| JetPack 1000 |
| JetPack 2000 |
+--------------+
2 rows in set (0.01 sec)
~~~

#### 分析

这里使用了正则表达式 **.000** 。==**.**== 是正则表达式语言中一个特殊的字符，==表示匹配任意一个字符==，因此，1000 和 2000 都匹配且返回。

当然，例2也可以用 LIKE 和通配符来完成。语句如下

~~~mysql
SELECT prod_name 
FROM products 
WHERE prod_name LIKE '%000' 
ORDER BY prod_name;
~~~



#### LIKE 与 REGEXP

> **LIKE 与 REGEXP** 之间有一个重要差别。以如下两条语句为例。
>
> ~~~mysql
> SELECT prod_name 
> FROM products 
> WHERE prod_name LIKE '1000' 
> ORDER BY prod_name;
> ~~~
>
> ~~~mysql
> SELECT prod_name 
> FROM products 
> WHERE prod_name REGEXP '1000' 
> ORDER BY prod_name;
> ~~~
>
> 如果执行上述两条语句，会发现第一条语句不返回数据，而第二条语句返回一行。
>
> * 原因：
>   * **LIKE 匹配整个列**。如果被匹配的文本在列值中作为部分出现，LIKE 将不会找到它，相应的行也不被返回（除非使用通配符）。
>   * **而 REGEXP 在列值内进行匹配**，如果匹配的文本在列值中出现，REGEXP 将会找到它，相应的行被返回。这是一个重要的差别。
>
> REGEXP 也能用来匹配整个列值（从而起与 LIKE 相同的作用），需要使用 **^** 和 **$** 定位符（anchor）即可。

#### 匹配不区分大小写

> MySQL中的正则表达式匹配（自版本3.23.4后）不区分大小写。
>
> 为区分大小写，可使用 BINARY 关键字。如
>
> ~~~mysql
> WHERE prod_name REGEXP BINARY 'JetPack .000'
> ~~~



### 9.2.2 进行 OR 匹配

为搜索两个串之一（或为一个串，或为另一个串），使用 **|** ，举例如下。

#### 输入

~~~mysql
SELECT prod_name
FROM products 
WHERE prod_name REGEXP '1000|2000' 
ORDER BY prod_name;
~~~

#### 输出

~~~bash
+--------------+
| prod_name    |
+--------------+
| JetPack 1000 |
| JetPack 2000 |
+--------------+
2 rows in set (0.00 sec)
~~~

#### 分析

语句使用了正则表达式 **1000|2000**。**|** 为正则表达式的 OR 操作符。它表示匹配其中之一。

> **两个以上的 OR 条件**
>
> ~~~mysql
> WHERE prod_name REGEXP '1000|2000|3000';	-- 将匹配1000或2000或3000
> ~~~



### 9.2.3 匹配几个字符之一

要匹配某一组字符中的任何单一字符没通过 **[]** 实现。

#### 输入

~~~mysql
SELECT prod_name 
FROM products
WHERE prod_name REGEXP '[123] Ton' 
ORDER BY prod_name;
~~~

#### 输出

~~~bash
+-------------+
| prod_name   |
+-------------+
| 1 ton anvil |
| 2 ton anvil |
+-------------+
2 rows in set (0.00 sec)
~~~

#### 分析

这里使用了正则表达式 **[123] Ton**。**[123]** 定义一组字符，它的意思是匹配1或2或3，因此，1 ton 和 2 ton 都匹配且返回。

**[] **是另一种形式的 OR 语句。事实上，正则表达式 **[123] Ton** 是 **[1|2|3] Ton** 的缩写。也可以使用后者。

**[]** 规定了内部 **|** 字符（如果有的话）的作用域。

字符集合也可以被否定，及匹配时排除指定的字符。使用方法：在集合开始处加一个 **^** 即可。例如：

~~~mysql
WHERE prod_name REGEXP '[^123] Ton'	-- 匹配除这些字符外的任何东西
~~~



### 9.2.4 匹配范围

可以用 **-** 来定义一个范围。举例如下

#### 输入

~~~mysql
SELECT prod_name 
FROM products 
WHERE prod_name REGEXP '[1-5] Ton' 
ORDER BY prod_name;
~~~

#### 输出

~~~bash
+--------------+
| prod_name    |
+--------------+
| .5 ton anvil |
| 1 ton anvil  |
| 2 ton anvil  |
+--------------+
3 rows in set (0.00 sec)
~~~

#### 分析

此处 **[1-5]** 定义了一个范围。**'[1-5] Ton'** 作为正则表达式进行匹配。



### 9.2.5 匹配特殊字符

如果想匹配具有特殊含义的特殊字符【如 . 、[] 、| 、和 - 等】，需要用 `\\`为前导。

**例**：

#### 输入

~~~mysql
SELECT vend_name 
FROM vendors 
WHERE vend_name REGEXP '\\.' 
ORDER BY vend_name;
~~~

#### 输出

~~~bash
+--------------+
| vend_name    |
+--------------+
| Furball Inc. |
+--------------+
1 row in set (0.00 sec)
~~~

#### 分析

原理为**转义（escaping）**。

`\\`也用来引用**元字符**（空白元字符——具有特殊含义的字符），如下标所示。

| 元字符 |   说明   |
| :----: | :------: |
| `\\f`  |   换页   |
| `\\n`  |   换行   |
| `\\r`  |   回车   |
| `\\t`  |   制表   |
| `\\v`  | 纵向制表 |

> **匹配 `\`** 
>
> 为了匹配反斜杠字符本身，需要使用`\\\`。
>
> **`\` 或 `\\`**
>
> 多数正则表达式实现用单个反斜杠转义特殊字符。但MySQL要求两个反斜杠（MySQL自己解释一个，正则表达式库解释另外一个）。



### 9.2.6 匹配字符类

为了方便工作，可以使用预定义的字符集，称为字符类（character class）。下标列出字符类及它们的含义。

|     类     |                         说明                         |
| :--------: | :--------------------------------------------------: |
| [:alnum:]  |          任意字母和数字（同 [a-zA-Z0-9] ）           |
| [:alpha:]  |               任意字符（同 [a-zA-Z] ）               |
| [:blank:]  |                空格和制表（同 `\\t`）                |
| [:cntrl:]  |          ASCII控制字符（ASCII 0到31和127）           |
| [:digit:]  |                任意数字（同 [0-9] ）                 |
| [:graph:]  |           与 [:print:] 相同，但不包括空格            |
| [:lower:]  |              任意小写字母（同 [a-z] ）               |
| [:print:]  |                    任意可打印字符                    |
| [:punct:]  |    即不在 [:alnum:] 又不在 [:cntrl:] 中的任意字符    |
| [:space:]  | 包括空格在内的任意空白字符（同 `[\\f\\n\\r\\t\\v]`） |
| [:upper:]  |              任意大写字母（同 [A-Z] ）               |
| [:xdigit:] |          任意十六进制数字（同 [a-fA-F0-9]）          |



### 9.2.7 匹配多个实例 

* 应用场景：
  * 有时候需要对匹配的数目进行更强的控制
  * 需要寻找所有的数，不管数中包含多少数字
  * 想寻找一个单词并且还能够适应一个尾随的s（如果存在）
  * 等等

这可以用下表列出的==**正则表达式重复元字符**==来完成。

| 元字符  |            说    明            |
| :-----: | :----------------------------: |
|   `*`   |         0个或多个匹配          |
|   `+`   | 1个或多个匹配（等于  `{1,}` ） |
|   `?`   | 0个或1个匹配（等于  `{0,1}` ） |
|  `{n}`  |         指定数目的匹配         |
| `{n,}`  |      不少于指定数目的匹配      |
| `{n,m}` |  匹配数目的范围（m不超过255）  |





**例1**：

#### 输入

~~~mysql
SELECT prod_name 
FROM products 
WHERE prod_name REGEXP '\\([0-9] sticks?\\)' 
ORDER BY prod_name;
~~~

#### 输出

~~~bash
+----------------+
| prod_name      |
+----------------+
| TNT (1 stick)  |
| TNT (5 sticks) |
+----------------+
2 rows in set (0.00 sec)
~~~

#### 分析

正则表达式 `\\([0-9] sticks?\\)` 说明：`\\(匹配(,[0-9]匹配任意数字（这个例子中为1和5）, sticks?匹配stick和sticks（s后的?使s可选，因为?匹配它前面的任何字符的0次或1次出现），\\)匹配)。` 如果没有？，就匹配stick和sticks会非常困难。



**例2**：匹配连在一起的4位数字

#### 输入

~~~mysql
SELECT prod_name 
FROM products
WHERE prod_name REGEXP '[[:digit:]]{4}' 
ORDER BY prod_name;
~~~

#### 输出

~~~bash
+--------------+
| prod_name    |
+--------------+
| JetPack 1000 |
| JetPack 2000 |
+--------------+
2 rows in set (0.00 sec)
~~~

#### 分析

**`[[:digit:]]`**匹配任意数字，因为它为数字的一个集合。**`{4}`**要求它**前面的字符**（这里即任意数字）**出现4次**，所以`[[:digit:]]{4}`匹配连在一起的任意4位数字。

注：上面的例子也可以用如下语句实现

#### 输入

~~~mysql
SELECT prod_name 
FROM products
WHERE prod_name REGEXP '[0-9][0-9][0-9][0-9]' 
ORDER BY prod_name;
~~~



### 9.2.8 定位符

* 应用场景
  * 为了匹配特定位置的文本。

需要使用下表列出的**定位符**。

|  元字符   |    说明    |
| :-------: | :--------: |
|    `^`    | 文本的开始 |
|    `$`    | 文本的结尾 |
| `[[:<:]]` |  词的开始  |
| `[[:>:]]` |  词的结尾  |



**例**：找出以一个数（或小数点）**开始**的所有产品。

#### 输入

~~~mysql
SELECT prod_name 
FROM products 
WHERE prod_name REGEXP '^[0-9\\.]' 
ORDER BY prod_name;	-- -- 匹配以数字或点开始的所有串
~~~

#### 输出

~~~bash
+--------------+
| prod_name    |
+--------------+
| .5 ton anvil |
| 1 ton anvil  |
| 2 ton anvil  |
+--------------+
3 rows in set (0.00 sec)
~~~

#### 分析

^ 匹配串的开始。因此，`^[0-9\\.]`只在 **.** 或任意数字为串中第1个字符时才匹配它们。没有 ^ 将会检索出串中间有数字的行。



#### 附1：^ 的双重用途

> **^ 的双重用途**：^ 有两种用法。在集合中（用`[和]`定义），用它来**否定该集合**
>
> ~~~mysql
> WHERE prod_name REGEXP '[^123] Ton'	-- 匹配除这些字符外的任何东西
> ~~~
>
> 否则，用来指串的开始处。
>
> ~~~mysql
> WHERE prod_name REGEXP '^[0-9\\.]'	-- 匹配以数字或点开始的所有串
> ~~~

#### 附2：使REGEXP起类似LIKE的作用

> LIKE和REGEXP不同之处在于：LIKE匹配整个串而REGEXP匹配字串。
>
> 利用定位符，通过**^开始**每个表达式，用**$结束**每个表达式，可以是REGEXP与LIKE一样。
>
> #### 输入
>
> ~~~MYSQL
> SELECT prod_id, prod_name 
> FROM products 
> WHERE prod_name LIKE '_ ton anvil' 
> ORDER BY prod_id;
> ~~~
>
> ~~~MYSQL
> -- 实现1
> SELECT prod_id, prod_name 
> FROM products 
> WHERE prod_name REGEXP '^. ton anvil$'
> ORDER BY prod_id;
> -- 实现2
> SELECT prod_id, prod_name 
> FROM products 
> WHERE prod_name REGEXP '^[[:digit:]]{1} ton anvil$'
> ORDER BY prod_id;
> ~~~
>
> #### 输出（一致）
>
> ~~~baash
> +---------+-------------+
> | prod_id | prod_name   |
> +---------+-------------+
> | ANV02   | 1 ton anvil |
> | ANV03   | 2 ton anvil |
> +---------+-------------+
> 2 rows in set (0.00 sec)
> ~~~

#### 附3：简单的正则表达式测试

> 可以在不使用数据库表的情况下用SELECT来测试正则表达式。REGEXP检查总是返回**0（没有匹配）**或**1（匹配）**。可以用带文字穿的REGEXP来测试表达式，并试验。相应语法如下：
>
> ~~~mysql
> SELECT 'hello' REGEXP '[0-9]';
> ~~~
>
> #### 输出
>
> ~~~bash
> +------------------------+
> | 'hello' REGEXP '[0-9]' |
> +------------------------+
> |                      0 |
> +------------------------+
> 1 row in set (0.00 sec)
> ~~~
>
> 因为文本'hello'中没有数字，所以返回0。





## 9.3 小结

* 正则表达式的基础知识
* 在MySQL的SELECT语句中使用REGEXP关键字









---



# 10. 创建计算字段

* 介绍什么是计算字段
* 如何创建计算字段
* 怎样从应用程序中使用别名引用它们





## 10.1 计算字段

问题背景：存储在数据库表中的数据一般不是应用程序所需要的格式。举例如下

- [ ] 想在**一个字段**中既**显示公司名**，又**显示公司的地址**，但这两个信息一般包含在不同的表列中。
- [ ] 城市、州和邮政编码存储在不同的列中（it should be），但邮件标签打印程序却需要把它们作为一个恰当格式的字段检索出来。
- [ ] 物品订单表存储物品的价格和数量，但不需要存储每个物品的总价格（用价格乘以数量即可）。为打印发票，**需要**物品的**总价格**。
- [ ] 需要根据表数据进行**总数**、**平均数计算**或其他计算。

上述例子中，存储在表中的数据都不是应用程序所需的。我们需要**直接从数据库中检索出转换、计算或格式化过的数据**；而不是直接检索出数据，然后再在客户机应用程序或报告程序中重新格式化。

**计算字段**可以解决此问题。与前面提到过的列不同，计算字段并不实际存在于数据库表中。**计算字段是运行时在SELECT语句内创建的**。

* **字段（field）**：基本上与列（column）的意思相同，经常互换使用，不过数据库列一般称为列，而术语**字段**通常用在**计算字段的连接**上。

> 注：只有数据库知道 SELECT 语句中哪些列是实际的表列，哪些列是计算字段。从客户机（如应用程序）的角度来看，计算字段的数据是以与其他列的数据相同的方式返回的。





## 10.2 拼接字段

* **拼接（concatenate）**：将值联结到一起构成单个值。

**例**：要生成一个供应商报表，需要在供应商表 vendors 中检索供应商信息，按照 name（location）的格式列出供应商位置。

解决办法：在MySQL的 SELECT 语句中，使用 **Concat()** 函数拼接两个列。

> **MySQL的不同之处**：许多DBMS使用+或 || 实现拼接，MySQL使用Concat()函数实现。

#### 输入

~~~mysql
SELECT Concat(vend_name, ' (', vend_country, ')') 
FROM vendors 
ORDER BY vend_name;
~~~

#### 输出

~~~bash
+--------------------------------------------+
| Concat(vend_name, ' (', vend_country, ')') |
+--------------------------------------------+
| ACME (USA)                                 |
| Anvils R Us (USA)                          |
| Furball Inc. (USA)                         |
| Jet Set (England)                          |
| Jouets Et Ours (France)                    |
| LT Supplies (USA)                          |
+--------------------------------------------+
6 rows in set (0.00 sec)
~~~

#### 分析

Concat()方法：用于拼接串，子串用逗号分隔，拼接后作为单个长串。



### 删除数据多余空格

* **Trim函数**：
  * MySQL 支持 **RTrim()** 去掉串右边的空格。
  * **LTrim()**  函数去掉串左边空格。
  * **Trim()** 去掉串左右两边的空格。

在第8节中提到通过**删除数据右侧多余的空格**来整理数据，可以使用MySQL的 **RTrim()** 函数来实现。如下所示：

#### 输入

~~~MySQL
SELECT Concat(RTrim(vend_name), ' (', RTrim(vend_country), ')') 
FROM vendors 
ORDER BY vend_name;
~~~

#### 分析

* RTrim() 函数去掉值右边的所有空格。通过使用RTrim() 函数， 各个列都进行了整理。



### 使用别名

适用场景：比如对于拼接字段形成的新列，没有名字、只有值。一个未命名的列不能用于客户机应用中，因为客户机没有办法引用它。

为解决该问题，SQL支持列别名。别名（alias）是一个字段或值的替换名。别名用AS关键字赋予。

**例**：对上一个搜索结果使用别名

#### 输入

~~~mysql
SELECT Concat(RTrim(vend_name), ' (', RTrim(vend_country), ')')  AS vend_title 
FROM vendors 
ORDER BY vend_name;
~~~

#### 输出

~~~bash
+-------------------------+
| vend_title              |
+-------------------------+
| ACME (USA)              |
| Anvils R Us (USA)       |
| Furball Inc. (USA)      |
| Jet Set (England)       |
| Jouets Et Ours (France) |
| LT Supplies (USA)       |
+-------------------------+
6 rows in set (0.00 sec)
~~~

#### 分析

`AS 别名` 指示 SQL 创建一个包含指定计算的名为 `别名` 的计算字段。任何客户机应用都可以按名引用这个列，就像它是一个实际的表列一样。

#### 别名的其他用途

> 别名常见的其他用途包括：
>
> * 在实际的表列名包含不符合规定的字符（如空格）时重新命名它
> * 在原来的名字含混或容易误解时扩充它
> * 等等

#### 导出列

> **别名**有时候**也称为导出列（derived column）**，不管称为什么，它们都是相同的东西。





## 10.3 执行算术计算

计算字段的另一个常见用途：对检索出的数据进行算术计算。

例：orders 表中包含收到的所有订单，orderitems表包含每个订单的各项物品。下面语句检索订单号 2005 中的所有物品

#### 输入

~~~mysql
SELECT prod_id, quantity, item_price 
FROM orderitems 
WHERE order_num = 20005;
~~~

#### 输出

~~~bash
+---------+----------+------------+
| prod_id | quantity | item_price |
+---------+----------+------------+
| ANV01   |       10 |       5.99 |
| ANV02   |        3 |       9.99 |
| TNT2    |        5 |      10.00 |
| FB      |        1 |      10.00 |
+---------+----------+------------+
4 rows in set (0.00 sec)
~~~

在后面增加一列汇总物品价格（单价乘以订购数量）

#### 输入

~~~mysql
SELECT 	prod_id, 
		quantity, 
		item_price, 
		quantity*item_price AS expanded_price 
FROM orderitems 
WHERE order_num = 20005;
~~~

#### 输出

~~~bash
+---------+----------+------------+----------------+
| prod_id | quantity | item_price | expanded_price |
+---------+----------+------------+----------------+
| ANV01   |       10 |       5.99 |          59.90 |
| ANV02   |        3 |       9.99 |          29.97 |
| TNT2    |        5 |      10.00 |          50.00 |
| FB      |        1 |      10.00 |          10.00 |
+---------+----------+------------+----------------+
4 rows in set (0.00 sec)
~~~

#### 分析

客户机应用现在可以正常使用新计算列 expanded_price。



MySQL支持下表列出的**基本算术操作符**。此外，圆括号可以用来区分优先顺序。

| 操作符 | 说    明 |
| :----: | :------: |
|   +    |    加    |
|   -    |    减    |
|   *    |    乘    |
|   /    |    除    |



#### 测试计算

> **如何测试计算**	SELECT提供了测试和实验函数与计算的一个很好的办法。可以省略FROM子句以简单地访问和处理表达式。例如：
>
> * SELECT 3*2 将返回6
> * SELECT Trim('abc') 将返回abc
> * SELECT Now() 利用Now()函数返回当前日期和时间。





## 10.4 小结

* 计算字段
  * 概念
  * 创建
* 拼接字段
  * Concat() 方法拼接子串
  * Trim() 方法去空格
* 使用别名
* 测试计算









---



# 11. 使用数据处理函数

* 什么是函数
* MySQL支持何种函数
* 如何使用这些函数





## 11.1 函数

函数的可移植性相较 SQL 来说要低一些。所以**使用函数**时，要保证**做好代码注释**，以便自己或去其他人能够确切知道所编写的 SQL 代码含义。





## 11.2 使用函数

大部分SQL支持以下类型的函数

* 处理文本串（如删除或填充值，转换值为大写或小写）的**文本函数**。
* 在数值数据上进行算术操作（如返回绝对值，进行代数运算）的**数值函数**。
* 处理日期和时间值并从这些值中提取特定成分（如，返回两个日期之差，检查日期有效性等）的**日期和时间函数**。
* 返回 DBMS 正使用的特殊信息（如返回用户登录信息，检查版本细节）的**系统函数**。





### 11.2.1 文本处理函数

例：使用Upper() 函数转大写。

#### 输入

~~~mysql
SELECT vend_name, Upper(vend_name) AS vend_name_upcase 
FROM vendors  
ORDER BY vend_name;
~~~

#### 输出

~~~bash
+----------------+------------------+
| vend_name      | vend_name_upcase |
+----------------+------------------+
| ACME           | ACME             |
| Anvils R Us    | ANVILS R US      |
| Furball Inc.   | FURBALL INC.     |
| Jet Set        | JET SET          |
| Jouets Et Ours | JOUETS ET OURS   |
| LT Supplies    | LT SUPPLIES      |
+----------------+------------------+
6 rows in set (0.01 sec)
~~~



下表列出了部分**常用的文本处理函数**

|    函数     |       说明        |
| :---------: | :---------------: |
|   Left()    | 返回串左边的字符  |
|  Length()   |   返回串的长度    |
|  Locate()   | 找出串的一个子串  |
|   Lower()   |  将串转换为小写   |
|   LTrim()   | 去掉串左边的空格  |
|   Right()   | 去掉串右边的字符  |
|   RTrim()   | 去掉串右边的空格  |
|  Soundex()  | 返回串的SOUNDEX值 |
| SubString() |  返回子串的字符   |
|   Upper()   |  将串转换为大写   |



### 11.2.2 日期和时间处理函数

日期和时间采用相应数据类型和特殊格式存储，以便快速有效地排序或过滤，并节省物理存储空间。

一般应用程序不能使用用来存储日期和时间地格式，所以使用十日起和时间函数来读取、统计和处理这些值。

下表为部分常用的日期和时间处理函数

|     函数      |              说明              |
| :-----------: | :----------------------------: |
|   AddDate()   |    增加一个日期（天、周等）    |
|   AddTime()   |    增加一个时间（时、分等）    |
|   CurDate()   |          返回当前日期          |
|   CurTime()   |          返回当前时间          |
|    Date()     |     返回日期时间的日期部分     |
|  DateDiff()   |        计算两个日期之差        |
|  Date_Add()   |     高度灵活的日期运算函数     |
| Date_Format() |  返回一个格式化的日期或时间串  |
|     Day()     |     返回一个日期的天数部分     |
|  DayOfWeek()  | 对于一个日期，返回对应的星期几 |
|    Hour()     |     返回一个时间的小时部分     |
|   Minute()    |     返回一个时间的分钟部分     |
|    Month()    |     返回一个时间的月份部分     |
|     Now()     |       返回当前日期和时间       |
|   Second()    |      返回一个时间的秒部分      |
|    Time()     |   返回一个日期时间的时间部分   |
|    Year()     |     返回一个日期的年份部分     |



* 注意：

  * MySQL使用的日期格式：`yyyy-mm-dd`

    ~~~mysql
    2005-09-01	-- 格式举例
    ~~~



**例1**：基本日期

#### 输入

~~~mysql
SELECT cust_id, order_num 
FROM orders 
WHERE order_date = '2005-09-01';
~~~

#### 输出

~~~bash
+---------+-----------+
| cust_id | order_num |
+---------+-----------+
|   10001 |     20005 |
+---------+-----------+
1 row in set (0.01 sec)
~~~

#### 分析

使用字符串比较并不可靠【因为不清楚列 order_date 的格式是否仅为 yyyy-mm--dd 】，更可靠的办法是使用 **Date(order_date)** 函数指示MySQL**仅提取 order_date 列的日期部分**，并**进行比较**。修改如下

#### 输入

~~~mysql
SELECT cust_id, order_num 
FROM orders 
WHERE Date(order_date) = '2005-09-01';
~~~



> 如果只要日期，使用 **Date()**
>
> 如果只要时间，使用 **Time()**
>
> Date() 和 Time() 都是MySQL4.1.1中第一次引入的。



**例2**：检索2005年9月下的所有订单。

方法1：

#### 输入

~~~mysql
SELECT cust_id, order_num 
FROM orders 
WHERE Date(order_date) BETWEEN '2005-09-01' AND '2005-09-30';
~~~

#### 输出

~~~bash
+---------+-----------+
| cust_id | order_num |
+---------+-----------+
|   10001 |     20005 |
|   10003 |     20006 |
|   10004 |     20007 |
+---------+-----------+
3 rows in set (0.00 sec)
~~~

方法2：

#### 输入

~~~mysql
SELECT cust_id, order_num 
FROM orders 
WHERE Year(order_date) = 2005 AND Month(order_date) = 9;
~~~

#### 分析

Year() 从日期中返回年份，Month() 从日期中返回月份。



### 11.2.3 数值处理函数

数字处理函数用于处理数值或数据，这些函数一般用于处理代数、三角或几何运算，因此没有串或日期-时间处理函数使用的那么频繁。

下表为常用**数值处理函数**。

|  函数  |        说明        |
| :----: | :----------------: |
| Abs()  | 返回一个数的绝对值 |
| Cos()  | 返回一个角度的预先 |
| Exp()  | 返回一个数的指数值 |
| Mod()  |  返回除操作的余数  |
|  Pi()  |     返回圆周率     |
| Rand() |   返回一个随机数   |
| Sin()  | 返回一个角度的正弦 |
| Sqrt() | 返回一个数的平方根 |
|  Tan   | 返回一个角度的正切 |





## 11.3 小结

* 使用 SQL  的数据处理函数
* 着重介绍了日期处理函数









---



# 12. 汇总数据

* 什么是SQL的聚集函数。
* 如何利用它们汇总表的数据。





## 12.1 聚集函数

有时候，需要汇总表的信息，而不需要表中的具体数据，如：

* 确定表中行数（或满足某个条件或包含某个特定值的行数）
* 获得表中行组的和
* 找出表列（或所有行或某些特定的行）的最大值、最小值、平均值

上述例子都需要对表中数据（而不是实际数据本身）汇总。因此，返回实际表数据是对时间和处理资源的浪费。为方便此类型的检索，MySQL给出了5个**聚集函数**。

* **聚集函数（aggregate function）**：运行在行组上，计算和返回单个值的函数。

下表给出聚集函数。

|  函数   |       说明       |
| :-----: | :--------------: |
|  AVG()  | 返回某列的平均值 |
| COUNT() |  返回某列的行数  |
|  MAX()  | 返回某列的最大值 |
|  MIN()  | 返回某列的最小值 |
|  SUM()  |  返回某列值之和  |

> MySQL还支持一系列的标准偏差聚集函数，这里并未涉及。



### 12.1.1 AVG()函数

AVG()可用来返回所有列的平均值，也可用来返回特定列或行的平均值。

**例1**：使用AVG()返回products表中所有产品的平均价格

#### 输入

~~~mysql
SELECT AVG(prod_price) AS avg_price 
FROM products;
~~~

#### 输出

~~~bash
+-----------+
| avg_price |
+-----------+
| 16.133571 |
+-----------+
1 row in set (0.00 sec)
~~~



**例2**：确定特定行或列的平均值

#### 输入

~~~mysql
SELECT AVG(prod_price) AS avg_price 
FROM products
WHERE vend_id = 1003; 
~~~

#### 输出

~~~bash
+-----------+
| avg_price |
+-----------+
| 13.212857 |
+-----------+
1 row in set (0.00 sec)
~~~



> **只能用于单个列**	AVG()只用于单个列数值的平均值计算，而且列名必须作为函数参数给出。为了获得多个列的平均值，必须使用多个AVG()函数。

> **NULL值**	AVG()函数忽略值为NULL的行。



### 12.1.2 COUNT()函数

**功能**：可利用COUNT()确定表中行的数目或符合特定条件的行的数目。

COUNT()函数有两种使用方式：

* 使用 **COUNT(*)** 对表中的行的数目进行技术，不管表列中包含的是控制（NULL）还是非空值。
* 使用 **COUNT(column)** 对特定列中具有值的行进行计数，忽略NULL值。



**例1**：返回 customers 表中客户的总数

#### 输入

~~~MySQL
SELECT COUNT(*) AS num_cust
FROM customers;
~~~

#### 输出

~~~bash
+----------+
| num_cust |
+----------+
|        5 |
+----------+
1 row in set (0.00 sec)
~~~

#### 分析

利用COUNT(*)计数，不管行中各列有什么值。

**例2**：对表中具有电子邮件地址的客户计数

#### 输入

~~~mysql
SELECT COUNT(cust_email) AS num_cust 
FROM customers;
~~~

#### 输出

~~~bash
+----------+
| num_cust |
+----------+
|        3 |
+----------+
1 row in set (0.00 sec)
~~~

#### 分析

此语句中，COUNT()函数指定了列名，则会忽略NULL值的列。

> **COUNT() 函数与NULL值**	如果**指定列名**，则指定列的值为空的行（即NULL值）被 COUNT() 函数**忽略**。但如果 COUNT() 函数中使用的是  **(*)** ，则 NULL 值也参与计数。



### 12.1.3 MAX()函数

MAX返回指定列中的最大值。MAX()要求指定列名

#### 输入

~~~mysql
SELECT MAX(prod_price) AS max_price 
FROM products;
~~~

#### 输出

~~~bash
+-----------+
| max_price |
+-----------+
|     55.00 |
+-----------+
1 row in set (0.00 sec)
~~~

> **对非数值数据使用MAX()**	在用于文本数据时，如果数据按相应的列排序，则MAX()返回最后一行。

> **NULL值**	MAX()函数









---



# 附1：MySQL关键字书写顺序、执行顺序



在SQL语句中每个关键字都会按照顺序往下执行，而每一步操作，会生成一个**虚拟表**，最后的虚拟表就是最终结果。

## 基本 SQL 语句书写顺序如下 :

~~~mysql
(8)SELECT (9)DISTINCT <select_list>
(1)FROM <left_table>
(3) <join_type> JOIN <right_table>
(2)       ON <join_condition>
(4)WHERE <where_condition>
(5)GROUP BY <group_by_list>
(6)WITH{CUBE|ROLLUP}
(7)HAVING <having_condition>
(10)ORDER BY <order_by_list>
(11)LIMIT <limit_number>
~~~



## 常用执行顺序

1. **`FROM`** ： 对FROM左边的表和右边的表计算**笛卡尔积**，产生虚表VT1；
2. **`ON`** ： 对虚拟表VT1进行ON筛选，只有那些符合条件的行才会被记录在虚拟表VT2中；
3. **`JOIN`** ：如果是OUT JOIN，那么将保留表中（如左表或者右表）未匹配的行作为外部行添加到虚拟表VT2中，从而产生虚拟表VT3；
4. **`WHERE`** ：对虚拟表VT3进行WHERE条件过滤，只有符合的记录才会被放入到虚拟表VT4；
5. **`GROUP BY`**：根据GROUP BY子句中的列，对虚拟表VT4进行分组操作，产生虚拟表VT5；
6. **`CUBE|ROLLUP`**：对虚拟表VT5进行CUBE或者ROLLUP操作，产生虚拟表VT6；
7. **`HAVING`** ：对虚拟表VT6进行 HAVING 条件过滤，只有符合的记录才会被插入到虚拟表VT7中；
8. **`SELECT`** ：执行SELECT操作，选择指定的列，插入到虚拟表VT8中；
9. **`DISTINCT`** ：对虚拟表VT8中的记录进行去重，产生虚拟表VT9；
10. **`ORDER BY`** ：将虚拟表VT9中的记录按照进行排序操作，产生虚拟表VT10；
11. **`LIMIT`** ：取出指定行的记录，产生虚拟表VT11，并将结果返回。



































