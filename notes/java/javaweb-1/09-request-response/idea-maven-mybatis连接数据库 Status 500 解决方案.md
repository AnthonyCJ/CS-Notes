# IDEA-MAVEN-Mybatis-Tomcat连接MySQL数据库Status-500解决方案

## 问题描述

在IDEA中使用内置MAVEN插件创建JAVA WEB项目，使用`pom.xml`配置了 mysql-connector-java、druid、mybatis、servlet、junit4、I/O工具类 commons-io、日志 org.slf4j。

为测试环境配置情况，我写了一个简单的ServletDemo，通过HTTPServlet的 `Request` 对象获取用户在浏览器页面输入信息并在本地数据库查询验证，通过 `Response` 对象写入查询结果，作为响应。

在启动Tomcat服务器运行之前，我写了一个测试类，自定义用户输入，直接通过MyBatis查询数据。运行正常且结果符合预期。

然后我启动Tomcat服务器，成功通过浏览器访问自定义的静态登录页面。输入用户名和密码后，单击提交按钮，页面出现如下报错信息。

![image-20211212104946362](solution/solution-1)

主要信息：

~~~java
org.apache.ibatis.exceptions.PersistenceException: 
### Error querying database.  Cause: java.sql.SQLException: Error setting driver on UnpooledDataSource. Cause: java.lang.ClassNotFoundException: Cannot find class: com.mysql.jdbc.Driver
### The error may exist in com/anthonycj/javaweb2021/requestdemo/mapper/UserMapper.xml
### The error may involve com.anthonycj.javaweb2021.requestdemo.mapper.UserMapper.select
### The error occurred while executing a query
### Cause: java.sql.SQLException: Error setting driver on UnpooledDataSource. Cause: java.lang.ClassNotFoundException: Cannot find class: com.mysql.jdbc.Driver
~~~



<br>

## 问题分析

注意到异常 `java.lang.ClassNotFoundException: Cannot find class: com.mysql.jdbc.Driver`，明显是数据库连接出了问题。这里我从以下几个角度开始排查：

* MAVEN的 `pom.xml` 是否配置了正确的依赖
  * `<dependency>` 的格式
  * `mysql-connector-java` 的<font color="ff9f44">**版本**</font>
* `mybatis-config.xml` 的配置信息是否正确
  * 包映射是否正确
  * 环境配置 `<environments>` 标签的内容是否正确
    * 连接数据库的参数是否正确、是否要指定时区 `useSSL=false&amp;serverTimezone=UTC`
      * 注意参数要用转义字符 `&amp;`，`&`符号在xml页面有其它语义
    * 参数前后是否有空格
    * 驱动名称、值的名称 `<property name="driver" value="com.mysql.jdbc.Driver"/>`
    * 登录数据库的用户名、密码是否正确
* Java代码是否存在问题
  * `com`路径下的`mapper`接口是否编写正确
  * `resources`路径下的`Mapper.xml`映射文件是否编写正确
  * Servlet代码是否编写正确

<br>

## 解决方案

最终确定是 `mysql-connector-java` 的版本问题。我的MySQL是 <font color="ff9f44">8.0.25</font> 版本，最初在 `pom.xml` 中引入5.1.46版本的连接依赖出现问题，最终引入 <font color="ff9f44">8.0.25</font> 版本的连接依赖后问题解决。引入坐标如下：

~~~xml
<!--mysql-connector-->
<!--8.0.25-->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.25</version>
</dependency>
~~~

<br>

## 总结-碎碎念

解决这个小bug花费了我14个小时的时间，让我很崩溃。

首先，我在出现问题的第一时间就联想到了mysql连接驱动的版本问题，也在第一时间就做了修改，使用了 **mysql 8.0.15** 版本的驱动进行连接，但还是连接失败，服务器返回 Status 500，经过短暂分析：“这个驱动的向后兼容性不可能做的这么差，8.0.15版本都不行，那 8.0.25 版本肯定也不行，所以应该不是驱动版本的问题”，**这让我直接排除了这个正确的错误原因，导致我在debug的路上一去不复返！！！**

然后，我检查代码，检查配置，编写测试类测试连接...

离谱的是我在测试类里可以正常连接数据库、但一使用Tomcat就无法连接数据库，和出错的状态只差**没启动Tomcat服务器、没在Servlet中运行同样的代码**。

其次，我开始怀疑是IDEA或者MAVEN插件的问题，我在**各种位置**导入本地的 mysql-connector jar包，分别在项目、模块、包下创建lib目录，仍然连接失败。

再次，我以为是拼写问题，所以我**重写了所有的代码**，包括xml配置文件、依赖坐标、Servlet类... 仍然是测试类能跑，服务器连接失败。

我在<font color="ff9f44">Google</font>搜索解决方案，在<font color="ff9f44">StackOverflow</font>搜索解决方案，基本上都是一些低级错误（如配置文件拼写错误、前后有空格等），与我的情况不一致。

最后，我没办法了，在CSDN【狗都不看】上找到了一条**好几个赞**解决方案，说什么**要把 连接驱动jar包 扔到Tomcat的lib目录下**，我一想这不是手动造异常呢？但我看这么多个<**赞**>，下面还有评论说<**亲测有用**>，走投无路的我只能试一下，果然不出所料，这个模块（就是我重写的项目模块）直接在密密麻麻的日志异常中<**彻底崩溃**>，再也<**无法启动**>。（那几个在下面点赞评论说有用的，我......）

我只能再次新建了一个模块，把环境重搭了一遍，那时候天都快亮了。

已经不能再往后了，我已经一点办法都没有了，所以准备重新过一遍之前的错误选项，更换连接驱动版本至 8.0.25，莫名其妙地解决问题。这时候我很懵，因为我记得昨天第一步尝试的就是更换驱动版本，而且记忆犹新——解决失败，然后我很不信邪地切换版本至 8.0.15，**居然能跑？！**

所以本次debug的过程就是南辕北辙【且思路清晰、意志坚定】，中间还有好心的CSDN网友提供热心帮助。

<br>

## 附：正确的配置文件

1. **pom.xml**

~~~xml
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>org.example</groupId>
  <artifactId>request-demo</artifactId>
  <version>1.0-SNAPSHOT</version>
  <!--打包方式-->
  <packaging>war</packaging>

  <properties>
    <maven.compiler.source>8</maven.compiler.source>
    <maven.compiler.target>8</maven.compiler.target>
  </properties>

  <!--依赖资源-->
  <dependencies>
    <!--mysql-connector-->
    <!--8.0.25-->
    <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>8.0.25</version>
    </dependency>

    <!--druid-->
    <dependency>
      <groupId>com.alibaba</groupId>
      <artifactId>druid</artifactId>
      <version>1.1.12</version>
    </dependency>

    <!--MyBatis-->
    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis</artifactId>
      <version>3.5.5</version>
    </dependency>

    <!--junit 4-->
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.13</version>
      <scope>test</scope>
    </dependency>

    <!--tomcat 7-->
    <dependency>
      <groupId>org.apache.tomcat.maven</groupId>
      <artifactId>tomcat7-maven-plugin</artifactId>
      <version>2.2</version>
    </dependency>

    <!--servlet 3.1-->
    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>javax.servlet-api</artifactId>
      <version>3.1.0</version>
      <scope>provided</scope>
    </dependency>

    <!--I/O工具类-->
    <dependency>
      <groupId>commons-io</groupId>
      <artifactId>commons-io</artifactId>
      <version>2.6</version>
    </dependency>

    <!--日志-->
    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-simple</artifactId>
      <version>1.6.6</version>
    </dependency>

  </dependencies>

  <build>
    <resources>
      <resource>
        <directory>src/main/resource</directory>
        <includes>
          <include>**/*.properties</include>
          <include>**/*.xml</include>
        </includes>
        <filtering>true</filtering>
      </resource>
      <resource>
        <directory>src/main/java</directory>
        <includes>
          <include>**/*.properties</include>
          <include>**/*.xml</include>
        </includes>
        <filtering>true</filtering>
      </resource>
    </resources>
  </build>
</project>
~~~

<br>

2. **mybatis-config.xml**

~~~xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!--起别名-->
    <typeAliases>
        <package name="com.anthonycj.javaweb2021.requestdemo.pojo"/>
    </typeAliases>

    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/db1?useSSL=false&amp;useServerPrepStmts=true"/>
                <property name="username" value="root"/>
                <property name="password" value="123456"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <!--扫描mapper-->
        <package name="com.anthonycj.javaweb2021.requestdemo.mapper"/>
    </mappers>
</configuration>
~~~

