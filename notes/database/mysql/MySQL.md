# MySQL

---

# 目录

1. 了解SQL
2. MySQL简介
3. 使用MySQL









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

* 行（row）：表中的一个记录。

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

该节介绍了SQL的一些基本术语和概念。

* 主键
  * 主键是关系表中记录的唯一标识。主键的选取非常重要：主键不要带有业务含义，而应该使用BIGINT自增或者GUID类型。主键也不应该允许`NULL`。
  * 可以使用多个列作为联合主键，但联合主键并不常用。







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

介绍了什么是MySQL，并引入了几个客户机实用程序。







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

本节内容涉及如何连接、登录MySQL，如何用USE选择数据库，如何用SHOW查看MySQL数据库、表和内部信息。







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



## 4.7 使用完全限定的表名



## 4.8 小结











