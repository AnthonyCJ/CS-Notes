# 4_中级SQL

## 4.3 事务

事务（transaction）由查询和（或）更新语句的序列组成。SQL标准规定当一条SQL语句被执行时，就隐式地开始了一个事务。下列SQL语句之一会结束该事务：

* `commit work` 提交当前事务：它使事务执行的更新在数据库中成为永久性的。在事务被提交后，一个新的事务会自动开始。
* `rollback work` 回滚当前事务：他会撤销事务中SQL语句执行的所有更新。因此，数据库状态被恢复到它执行该事务的第一条语句之前的状态。

> 关键字 `work` 在两条语句中都是可选的。

如果在一个事务的执行期间检测到某种错误状态，事务回滚是有用的。

> 在某种意义上，提交类似于对正在编辑的文档保存更改，而回滚类似于退出编辑会话且不保存更改。

一旦一个事务执行了 `commit work` ，它的影响就不能再用 `rollback work` 来撤销了。

<br><br>

## 4.4 完整性约束

<font color="ff9f44">**完整性约束（integrity constraint）**</font>保证授权用户对数据库所做的修改不会导致数据一致性的丢失。因此，完整性约束防止的是对数据的意外破坏。

> 这与安全性约束（security constraint）不同，安全性约束防止未经授权的用户访问数据库。

完整性约束的示例：

* 教师姓名不能为 `null`
* 任意两位教师不能有相同的教师ID
* `course` 关系中的每个系名必须在 `department` 关系中有一个对应的系名。
* 一个系的预算必需大于0.00美元

在3.2.2节中出现过完整性约束的一些形式，在本节中将出现完整性约束的更多形式。在第7章中，将涉及一种被称作<font color="ff9f44">**函数依赖（functional dependency）**</font>的完整性约束形式。它主要应用在模式设计的过程中。

完整性约束通常被视为数据库模式设计过程的一部分，并作为用于创建关系的 `create table` 命令的一部分被声明。然而，也可以通过使用 `alter table table-name add constraint` 命令将完整性约束施加到已有关系上，其中 `constraint` 可以是该关系上的任意约束。

> 当这样一条命令被执行时，系统首先保证该关系满足指定的约束。如果满足，那么约束被施加到关系上；如果不满足，则上述命令被拒绝执行。

<br>

### 4.4.1 单个关系上的约束

`create table` 命令不仅可以用来定义关系表，还可以包括完整性约束语句。除了主码约束 `primary key` 外，允许的完整性约束包括：

* `not null` 
* `unique` 
* `check` 

以下将分别讲解这些类型的约束。

<br>

### 4.4.2 非空约束

<font color="ff9f44">非空（not null）</font>约束禁止对该属性插入空值，并且他是域约束（domain constraint）的一个示例。可能导致向一个声明为非空的属性插入空值的任何数据库修改都会产生错误诊断信息。

**例：**限定 `name` 和 `budget` 属性的域来排除空值。

~~~sql
name varchar(20) not null
budget numeric(12,2) not null
~~~

<br>

### 4.4.3 唯一性约束

**例：**SQL还支持如下完整性约束

~~~sql
unique(A1,A2,...,Am)
~~~

<font color="ff9f44">唯一性（unique）</font>声明指出属性 `A1,A2,...,Am` 形成了一个超码；即关系中没有两个元组能在所有列出的属性上取值相同。

> 声明了唯一性的属性允许为null，除非它们已被显式地声明为非空。

<br>

### 4.4.4 check 子句

当应用于关系声名时，check ( P ) 子句指定一个谓词P, 关系中的每个元组都必须满足谓词P 。

通常用 `check` 子句来保证属性值满足指定的条件，实际上创建了一个强大的类型系统。例如，在创建关系 `department` 的 `create table` 命令中的`check (budget > 0 )` 子句将保证 `budget` 上的取值是非负的。

作为另一个示例，见如下语句：

~~~sql
create table section
    (course_id 		varchar(8),
    sec_id 			varchar( 8),
    semester 		varchar(6),
    year 			numeric(4, 0),
    building 		varchar(15),
    room_number 	varchar(7),
    time＿slot＿id   varchar(4),
    primary key (course_id,sec＿id,semester,year),
    check(semester in ('Fall','Winter','Spring','Summer')));
~~~

这里我们用 `check` 子句模拟了一个枚举类型，通过指定 `semester` 必须是 'Fall ' 、'Winter' 、,'Spring' 或 'Summer' 中的一个来实现。这样， `check `子句允许以有力的方式对属性域加以限制， 这是大多数编程语言的类型系统都不能允许的。

> 根据SQL 标准， check 子句中的谓词可以是包括子查询在内的任意谓词。然而，当前还没有一个广泛使用的数据库产品允许包含子查询的谓词。

<br>

### 4.4.5 引用完整性

我们常常希望保证在一个关系（引用关系）中给定属性集上的取值也在另一关系（被引用关系）的特定属性集的取值中出现。这种情况称为<font color="ff9f44">**引用完整性( referential integrity )约束**</font> ，外键是引用完整性约束的一种形式，其中被引用的属性构成被引用关系的主键。

例：建表+外键声明

~~~sql
create table classroom
    ( building varchar ( 15 ) ,
    room_number varchar ( 7 ) ,
    capacity numeric ( 4 , 0) .
    primary key ( building, room_number ) );
    
create table department
    ( dept_name varchar ( 20 ) ,
    building varchar ( 15 ) ,
    budget numeric ( 12 , 2 ) check ( budget > 0 ) ,
    primary key ( dept_name) );
    
create table course
    ( course_id varchar ( 8 ) ,
    title varchar ( 50 ) ,
    dept_name varchar ( 20 ) ,
    credit.J numeric ( 2 , 0 ) check ( credits > 0) ,
    primary key ( course_id) ,
    foreign key ( dept_name) references department );
    
create table instructor
    ( ID varchar ( 5 ) ,
    name varchar ( 20 ) , not null
    dept_name varchar ( 20 ) ,
    salary numeric ( 8 , 2 ) , check ( salary > 29000 ),
    primary key ( ID ) ,
    foreign key ( dept_name ) references department );
    
create table section
    ( course_id varchar ( 8 ) ,
    sec_id varchar ( 8 ) ,
    semester varchar ( 6 ) , check ( semester in
    								(' Fall' , 'Winter', 'Spring', 'Summer' )),
    year numeric ( 4 , 0 ) , check ( year > 1759 and year < 2100 ),
    building varchar ( 1 5 ) ,
    room_number varchar ( 7 ) ,
    time_slot_id varchar ( 4 ) ,
    primary key ( course_id, sec_id, semester, year) ,
    foreign key ( course_id ) references course,
    foreign key ( building , room_number) references classroom );
~~~

如上所示，`course` 表的定义中有一个声明 `foreign key (dept_name) references department` 。这个外码声明表示：对于每个课程元组，元组中指定的系名 `dept_name` 必须在 `department` 关系中存在。如果没有这个约束，可能会为一门课指定一个并不存在的系名。



















