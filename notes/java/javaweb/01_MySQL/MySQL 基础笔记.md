# MySQL 基础笔记

---

## 目录

1. 数据库基本概念
2. MySQL数据库软件
   1. 安装
   2. 卸载
   3. 配置
3. SQL



---

## 1. 数据库基本概念

1. 数据库：DataBase 简称：DB
2. 什么是数据库？
   * 用于存储和管理数据的仓库。
3. 数据库的特点：
   1. 持久化存储数据。数据库是一个文件系统。
   2. 方便存储和挂历数据。
   3. 使用统一的方式操作数据——SQL
4. 常见的数据库软件



---

## 2. MySQL数据库软件

1. 安装

   

2. 卸载

   1. 在mysql的安装目录找到my.ini文件
      * 复制 datadir=D:\SourceFiles\mysql\mysql-8.0.25-winx64\data
   2. 卸载MySQL
   3. 删除C:/ProgramData目录下的MySQL文件夹。

3. 配置

   * MySQL服务的启动

     1. 手动。
     2. cmd --> services.msc  // 打开服务的窗口
     3. 使用管理员打开cmd
        * net start mysql：启动mysql的服务
        * net stop mysql：关闭mysql服务

   * MySQL登录

     1. mysql -uroot -p + 输入密码 【默认连接本地 MySQL】
     2. mysql -hip -uroot -p连接目标的密码【本机IP为：127.0.0.1】
     3. mysql --host=ip --user=root --password=连接目标的密码

   * MySQL退出

     1. exit
     2. quit

   * MySQL目录结构

     1.  MySQL安装目录：basedir="D:/develop/MySQL/"

        * bin：二进制可执行文件
        * data：数据目录
        * include：存放c语言的一些头文件
        * lib：库文件
        * share：MySQL的错误信息

        *  ==**配置文件**== my.ini
        * 

     2. MySQL数据目录：datadir=D:\SourceFiles\mysql\mysql-8.0.25-winx64\data

        * 几个概念
          * 数据库：文件夹
          * 表：文件
          * 数据：数据



---

## 3. SQL

### 3.1 什么是SQL？

​	Structured Query Language：==结构化查询语言==

​	==定义了操作所有关系型数据库的规则==。每一种数据库操作的方式存在不一样的地方，类似“方言”。

### 3.2 SQL通用语法

1. SQL 语句可以单行或多行书写，以**分号结尾**。
2. 可使用空格和缩进来增强语句的可读性。
3. MySQL 数据库的 SQL 语句==不区分大小写==，**关键字建议**使用**大写**。
4. 3 种注释
   * 单行注释: 
     * --空格
     *  #注释内容(mysql 特有) 
   *  多行注释: /* 注释 */

### 3.3 SQL分类

1. DDL(Data Definition Language)数据==定义==语言

   ​	用来定义数据库对象：数据库，表，列等。关键字：create, drop,alter 等

2. DML(Data Manipulation Language)数据操作语言
   	用来对数据库中表的数据进行==增删改==。关键字：insert, delete, update 等

3. DQL(Data Query Language)数据==查询==语言
   	用来查询数据库中表的记录(数据)。关键字：select, where 等

4. DCL(Data Control Language)数据控制语言(==授权==)
   	用来定义数据库的访问权限和安全级别，及创建用户。关键字：GRANT， REVOKE 等



---

## 4. DDL：操作数据库、表

### 4.1 操作数据库：CRUD

1. C(Create):创建

   * 创建数据库：

     ~~~mysql
     	create database 数据库名称;
     ~~~

   * 创建数据库，判断不存在，再创建：

     ~~~mysql
     	create database if not exists 数据库名称;	
     ~~~

   * 创建数据库，并指定字符集：

     ~~~mysql
     	create database 数据库名称 character set 字符集名;
     ~~~

   * 练习： 创建db4数据库，判断是否存在，并制定字符集为gbk

     ~~~mysql
     	create database if not exists db4 character set gbk;
     ~~~

2. R(Retrieve)：查询

   * 查询所有数据库名称：

     ~~~mysql
     	show databases;
     ~~~

   * 查询某个数据库的创建语句【可查到某个数据库的字符集】

     ~~~mysql
     	show create database 数据库名称;
     ~~~

3. U(Update):修改

   * 修改数据库的字符集

     ~~~mysql
     	alter database 数据库名称 character set 字符集名称;
     ~~~

4. D(Delete):删除 

   * 删除数据库

     ~~~mysql
     	drop database 数据库名称;
     ~~~

   * 判断数据库存在，存在再删除

     ~~~mysql
     	drop database if exists 数据库名称;
     ~~~

5. 使用数据库

   * 查询当前正在使用的数据库名称

     ~~~mysql
     	select database();
     ~~~

   * 使用数据库

     ~~~mysql
     	use 数据库名称;
     ~~~

     



### 4.2 操作表











