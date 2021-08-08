# MySQL 基础

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
     	CREATE DATABASE 数据库名称;
     ~~~

   * 创建数据库，判断不存在，再创建：

     ~~~mysql
     	CREATE DATABASE IF NOT EXISTS 数据库名称;	
     ~~~

   * 创建数据库，并指定字符集：

     ~~~mysql
     	CREATE DATABASE 数据库名称 CHARACTER SET 字符集名;
     ~~~

   * 练习： 创建db4数据库，判断是否存在，并制定字符集为gbk

     ~~~mysql
     	CREATE DATABASE IF NOT EXISTS db4 CHARACTER SET gbk;
     ~~~

2. R(Retrieve)：查询

   * 查询所有数据库名称：

     ~~~mysql
     	SHOW DATABASES;
     ~~~

   * 查询某个数据库的创建语句【可查到某个数据库的字符集】

     ~~~mysql
     	SHOW CREATE DATABASES 数据库名称;
     ~~~

3. U(Update):修改

   * 修改数据库的字符集【UTF8, UTF8MB4, GBK

   * 】

     ~~~mysql
     	ALTER DATABASE 数据库名称 CHARACTER SET 字符集名称;
     ~~~

4. D(Delete):删除 

   * 删除数据库

     ~~~mysql
     	DROP DATABASE 数据库名称;
     ~~~

   * 判断数据库存在，存在再删除

     ~~~mysql
     	DROP DATABASE IF EXISTS 数据库名称;
     ~~~

5. 使用数据库

   * 查询当前正在使用的数据库名称

     ~~~mysql
     	SELECT DATABASE();
     ~~~

   * 使用数据库

     ~~~mysql
     	USE 数据库名称;
     ~~~

     



### 4.2 操作表

#### 4.2.1 C(Create):创建

1. 语法：

   ~~~mysql
   	CREATE TABLE 表名(
           列名1 数据类型1,
           列名2 数据类型2,
           ....
   		列名n 数据类型n
       );
   ~~~
   
   * **注意：最后一列，不需要加逗号（,）**
   
   * 数据库类型：
   
     1. int：整数类型
   
        ~~~mysql
        	age int,
        ~~~
   
     2. double:小数类型【小数总位数, 小数点后位数】
   
        ~~~mysql
        	score double(5,2),
        ~~~
   
     3. date:日期，只包含年月日，yyyy-MM-dd
   
     4. datetime:日期，包含年月日时分秒	 yyyy-MM-dd HH:mm:ss
   
     5. timestamp:**时间戳类型**	包含年月日时分秒	 yyyy-MM-dd HH:mm:ss	
   
        * 如果将来不给这个字段赋值，或赋值为null，则默认使用当前的系统时间，来自动赋值
   
     6. varchar：==字符串==
   
        * name varchar(20):姓名**最大长度**20个字符
        * zhangsan 8个字符  张三 2个字符
   
   * 创建表：
   
     ~~~mysql
     	CREATE TABLE student(
     		id int,
     		name varchar(32),
     		age int,
     		score double(4,1),
     		birthday date,
     		insert_time timestamp DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP;
     	);
     ~~~
   
   * 复制表：
   
     ~~~mysql
     	create table 表名 like 被复制的表名;	
     ~~~

#### 4.2.2 R(Retrieve)：查询

* 查询某个数据库中所有的表名称

  ~~~mysql
  	SHOW TABLES;
  ~~~

* 查询表结构

  ~~~mysql
  	DESCRIBE 表名;
  ~~~

#### 4.2.3 U(Update):修改

1. 修改表名

   ~~~mysql
   	ALTER TABLE 表名 RENAME TO 新的表名;
   ~~~

2. 修改表的字符集

   ~~~mysql
   	ALTER TABLE 表名 CHARACTER SET 字符集名称;
   ~~~

3. 添加一列

   ~~~mysql
   	ALTER TABLE 表名 ADD 列名 数据类型;
   ~~~

4. 修改列名称 类型

   ~~~mysql
   	ALTER TABLE 表名 CHANGE 列名 新列m 新数据类型;
   	ALTER TABLE 表名 MODIFY 列名 新数据类型; -- 只修改列类型
   ~~~

5. 删除列

   ~~~mysql
   	ALTER TABLE 表名 DROP 列名;
   ~~~

#### 4.2.4 D(Delete):删除

~~~mysql
	DROP TABLE 表名;
	DROP TABLE IF EXISTS 表名 ;
~~~

* 客户端图形化工具：SQLYog、DataGrip



---



## 5. DML：增删改表中数据【重点】 

### 5.1 添加数据：

* 语法：

  ~~~mysql
  INSERT INTO 表名(列名1,列名2,...列名n) VALUES(值1,值2,...值n);
  ~~~

* 注意：

  1. 列名和值要一一对应。

  2. 如果表名后，不定义列名，则默认给所有列添加值

     ~~~mysql
     INSERT INTO 表名 VALUES(值1,值2,...值n);
     ~~~

  3. **除了数字**类型，==其他类型需要使用引号==(单双都可以)引起来

### 5.2 删除数据：

* 语法：

  ~~~mysql
  	DELETE FROM 表名 WHERE 条件 -- 如果不加条件将默认删
  	除表所有数据
  ~~~

* **注意**：

  1. 如果不加条件，则删除表中所有记录。

  2. 如果要删除所有记录

     ~~~mysql
     	TRUNCATE TABLE 表名; -- 推荐使用。【高效】 先删除表，然后再创建一张一样的表
     	DELETE FROM 表名; -- 不推荐使用。【低效】有多少条记录就会执行多少次删除操作
     ~~~

### 5.3 修改数据：

* 语法：

  ~~~mysql
  	UPDATE 表名 SET 列名1 = 值1, 列名2 = 值2,... WHERE 条件;
  ~~~

* **注意**：

  * 如果不加任何条件，则会将表中所有记录全部修改。



---

## 6. DQL：查询表中的记录

~~~mysql
	SELECT * FROM 表名;
~~~

### 6.1 查询语法

* SELECT
  * 字段列表
* FROM
  * 表名列表
* WHERE
  * 条件列表
* GROUP BY
  * 分组字段【查询男生分组、女生分组相关】
* HAVING
  * 分组之后的条件
* ORDER BY
  * 排序
* LIMIT
  * 分页限定

### 6.2 基础查询

1. 多个字段的查询

   ~~~mysql
   	SELECT 字段名1，字段名2... FROM 表名;
   ~~~

   * 注意：
     * 如果查询所有字段，则可以使用 * 来替代字段列表。

2. 去除重复

   ~~~mysql
   	SELECT DISTINCT 字段 FROM 表名;
   ~~~

3. 计算列

   * 一般可以使用四则运算计算一些列的值。（一般只会进行数值型的计算）			

   ~~~mysql
   	-- 计算 math 和 english 字段分数之和【要求显示 name, math, english, math+english 字段】
   	SELECT name, math, english, math + english FROM student;
   	-- 对 english 字段可能为NULL的情况作处理
   	SELECT name, math, english, math + IFNULL(english, 0) FROM student;
   	
   	IFNULL(表达式1,表达式2) -- NULL参与的运算，计算结果都为NULL，可以使用IFNULL()表达式替换为某值
   		-- 表达式1：哪个字段需要判断是否为NULL
   		-- 如果该字段为 NULL 后的替换值。
   ~~~

4. 起别名

   ~~~mysql
   	原字段名 AS 别名 -- AS 也可以省略
   	-- 例
   	SELECT name, math, english, math + IFNULL(english, 0) AS 总分 FROM student;
   	SELECT name, math, english, math + IFNULL(english, 0) 总分 FROM student;	-- 省略AS
   ~~~

### 6.3 条件查询

1. WHERE 子句后跟条件
2. 运算符
   * <、> 、<= 、>= 、= 、<>
   * BETWEEN...AND 【包含左右区间】
   * IN (集合) 
   * LIKE：模糊查询
     * 占位符：
       * _：单个任意字符
       * %：多个任意字符
   * IS NULL  
   * AND  或 &&
   * OR  或 ||
   * NOT  或 !

~~~mysql
	-- 查询年龄大于20岁
	SELECT * FROM student WHERE age > 20;
	SELECT * FROM student WHERE age >= 20;
	
	-- 查询年龄等于20岁
	SELECT * FROM student WHERE age = 20;
	
	-- 查询年龄不等于20岁
	SELECT * FROM student WHERE age != 20;
	SELECT * FROM student WHERE age <> 20;
	
	-- 查询年龄大于等于20 小于等于30
	SELECT * FROM student WHERE age >= 20 && age <= 30;
	SELECT * FROM student WHERE age >= 20 AND age <= 30;
	SELECT * FROM student WHERE age BETWEEN 20 AND 30;	-- 包含左右区间
	
	-- 查询年龄22岁，18岁，25岁的信息
	SELECT * FROM student WHERE age = 22 OR age = 18 OR age = 25;
	SELECT * FROM student WHERE age IN (22, 18, 25);
	
	-- 查询英语成绩为 NULL
	SELECT * FROM student WHERE english IS NULL;	-- 注意：NULL 值不能使用 = （!=） 判断
	
	-- 查询英语成绩不为 NULL
	SELECT * FROM student WHERE english IS NOT NULL;
	
	-- 查询姓马的有哪些？ LIKE
	SELECT * FROM student WHERE name LIKE '马%';
	
	-- 查询姓名第二个字是化的人
	SELECT * FROM student WHERE name LIKE '_化%';
	
	-- 查询姓名是3个字的人
	SELECT * FROM student WHERE name LIKE '___';
	
	-- 查询姓名中包含德的人
	SELECT * FROM student WHERE name LIKE '%德%';
~~~
