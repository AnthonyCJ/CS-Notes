# Servlet & HTTP & Request

---

## 目录

1. Servlet
2. HTTP
3. Request



---

## 1. Servlet

1. 概念Servlet3.0 注解配置

2. 步骤

3. 执行原理

4. 生命周期

5. Servlet3.0 注解配置

6. Servlet的体系结构	

   Servlet -- 接口
   		|
   	GenericServlet -- 抽象类
   		|
   	HttpServlet  -- 抽象类

   * **GenericServlet**：将Servlet接口中其他的方法做了默认空实现，只将**service()**方法作为抽象
     * 将来定义Servlet类时，可以继承GenericServlet，实现service()方法即可
   * **HttpServlet**：对http协议的一种封装，简化操作
     1. 定义类继承 **HttpServlet**
     2. 复写 **doGet()** / **doPost()** 方法

7. Servlet相关配置

   1. urlpartten:Servlet访问路径
      1. 一个Servlet可以定义多个访问路径 ： @WebServlet({"/d4","/dd4","/ddd4"})
      2. 路径定义规则：
         1. /xxx：路径匹配
         2. /xxx/xxx:多层路径，目录结构
         3. *.do：扩展名匹配



---

## 2. HTTP

### 2.1 概念

* HTTP：Hyper Text Transfer Protocol 超文本传输协议
* 传输协议：定义了，客户端和服务器端通信时，发送数据的格式
* 特点：
  1. **基于TCP/IP的高级协议**
  
  2. 默认端口号:80
  
     > HTTP协议的默认端口号也是80
  
  3. 基于 **请求 / 响应模型** 的：一次请求对应一次响应
  
  4. 无状态的：每次请求之间相互独立，不能交互数据
* 历史版本
  * 1.0：每一次请求响应都会建立新的连接
  * 1.1：复用连接

### 2.2 请求消息数据格式

1. **请求行**

   ​		请求方式 请求url 请求协议/版本
   ​		GET / login.html	HTTP/1.1

   * 请求方式：
     * HTTP协议有7中请求方式，常用的有2种
       * **GET**
         1. **请求参数在请求行中，在url后**
         2. 请求的url长度有限制的
         3. 不太安全
       * **POST**
         1. **请求参数在请求体中**
         2. 请求的url长度没有限制的
         3. 相对安全

2. **请求头**：客户端浏览器告诉服务器一些信息

   * 格式：

     ​	请求头名称: 请求头值

   * 常见的请求头：
     1. Host：表示请求的主机【示例为 localhost】
     2. **User-Agent**：浏览器告诉服务器，访问浏览器使用的**浏览器版本信息**
        * 可以在服务器端获取该头的信息，解决**浏览器的兼容性问题**
     3. Accept：告知服务器，【我】作为浏览器可以接收什么么样的响应信息格式
     4. Accept-Language：支持的语言环境
     5. Accept-Encoding：可以接收的压缩格式
     6. **Referer**：http://localhost/login.html
        * 告诉服务器，我(当前请求)从哪里来？
          * 作用
            1. **防盗链**
            2. **统计工作**
     7. Connection：keep-alive 标示该链接一直处于 alive 状态，可复用
     8. Upgrade-Insecure-Requests：关于升级的信息【了解即可】

3. **请求空行**

   * 空行，就是用于分割POST请求的请求头，和请求体的。

4. **请求体**（正文）：

   * 封装POST请求消息的请求参数

5. 字符串格式

   ```http
   POST /login.html	HTTP/1.1
   Host: localhost 
   User-Agent: Mozilla/5.0 (Windows NT 6.1; Win64; x64; rv:60.0) Gecko/20100101 Firefox/60.0
   Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
   Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
   Accept-Encoding: gzip, deflate
   Referer: http://localhost/login.html
   Connection: keep-alive
   Upgrade-Insecure-Requests: 1
   
   username=zhangsan	
   ```

* 响应消息数据格式



---

## 3. Request

### 3.1 原理

* request对象和response对象的**原理**
  1. request和response对象是**由服务器创建**的。我们来使用它们
  2. **request**对象是来**获取请求消息**，**response**对象是来**设置响应消息**

### 3.2 request对象继承体系结构：	

​	ServletRequest		--	接口【在抽象类GenericServlet中定义】
​		|	继承
​	HttpServletRequest	-- 接口【在抽象类HttpServlet中定义】
​		|	实现
​	org.apache.catalina.connector.RequestFacade 【在类(tomcat)中实现】

### 3.3 request功能

1. 获取请求消息数据
	1. **获取请求行数据**
		* GET /day14/demo1?name=zhangsan HTTP/1.1
		* 方法：
			1. 获取请求方式 ：GET
				* **String getMethod()**  
			2. ==获取虚拟目录==：/day14
				* **String getContextPath()**
			3. 获取Servlet路径: /demo1
				* **String getServletPath()**
			4. 获取get方式请求参数：name=zhangsan
				* **String getQueryString()**
			5. ==获取请求URI==：/day14/demo1
				* **String getRequestURI():		/day14/demo1**
				* **StringBuffer getRequestURL()  :http://localhost/day14/demo1**

				* URL:统一资源定位符 ： http://localhost/day14/demo1	中华人民共和国
				* URI：统一资源标识符 : /day14/demo1					共和国
			
			6. 获取协议及版本：HTTP/1.1
				* **String getProtocol()** 【在ServletRequest中定义】

			7. 获取客户机的IP地址：
				* **String getRemoteAddr()**
		
	2. **获取请求头数据**
		* 方法：
			* (***)String getHeader(String name)**:通过请求头的名称==获取请求头的值==
			* **Enumeration<String> getHeaderNames()**:获取所有的请求头名称
		
	3. 获取请求体数据:
		* 请求体：只有POST请求方式，才有请求体，在请求体中封装了POST请求的请求参数
		* 步骤：
			1. 获取流对象
				*  **BufferedReader getReader()**：获取字符输入流，只能操作字符数据
				*  **ServletInputStream getInputStream()**：获取字节输入流，可以操作所有类型数据
					* 在文件上传知识点后讲解
			2. 再从流对象中拿数据
	
2. 其他功能：

   1. ==获取请求参数**通用方式**==：不论get还是post请求方式都可以使用下列方法来获取请求参数

      > 好处：doGet() 和 doPost() 方法代码可复用。
      >
      > 方法：
      >
      > ​		在 doPost() 中调用 this.doGet(request, response) 或
      >
      > ​		在 doGet() 中调用 this.doPost(request, response)
   
      1. 【==较常用==】**String getParameter(String name)**:根据参数名称**获取参数值**    

         > username = zs, password = 123 参数 name = zs，得到zs
         >
         > 由键名获取值
   
         ~~~java
         // 1.根据参数名称获取参数值
         String username = request.getParameter("username");
         System.out.println("post: ");
         System.out.println(username);
         ~~~
   
         
   
      2. **String[] getParameterValues(String name)**:根据参数名称**获取参数值**的数组  
   
         >  hobby = xx & hobby = game 参数name = hobby, 返回数组。**多用于复选框**
         >
         > 数组获取的是勾选的复选框的值

         ~~~java
         // 2.根据参数名称获取参数值的数组【复选框案例】
         String[] hobbies = request.getParameterValues("hobby");
         for (String hobby : hobbies) {
             System.out.println(hobby);
         }
         ~~~
   
         
   
      3. **Enumeration<String> getParameterNames()**:获取请求的所有参数名称
   
         > 获取所有的键
   
         ~~~java
         // 3.获取请求的所有参数名称
         Enumeration<String> parameterNames = request.getParameterNames();
         while (parameterNames.hasMoreElements()) {
             // 获取参数名称
             String parameterName = parameterNames.nextElement();
             System.out.println(parameterName);
             // 获取参数值
             String value = request.getParameter(parameterName);
             System.out.println(value);
             System.out.println("---------------");
         }
         ~~~
   
         
   
      4. 【==常用==】**Map<String,String[]> getParameterMap()**:获取所有参数的map集合
   
         ~~~java
         // 4.获取所有参数的Map集合
         Map<String, String[]> parameterMap = request.getParameterMap();
         // 遍历
         Set<String> keySet = parameterMap.keySet();
         for (String name : keySet) {
             String[] values = request.getParameterValues(name);
             System.out.println(name);
             for (String value : values) {
                 System.out.println(value);
             }
             System.out.println("--------------");
         }
         ~~~
   
         
   
      * 获取请求参数过程中可能遇到中文乱码问题：
        * get方式：tomcat 8 已经将get方式乱码问题解决了
        * post方式：会乱码
          * 解决：在获取参数前，设置request的编码request.setCharacterEncoding("utf-8");
        
        ~~~java
        // 1.设置流的编码[编码应与发送post请求的页面编码格式一致]
        request.setCharacterEncoding("utf-8");
        
        // post 获取请求参数 username
        String userName = request.getParameter("username");
        System.out.println(userName);
        ~~~
        
        
   
   2. ==请求转发==：一种在服务器内部的资源跳转方式
   
      1. 步骤：
         1. 通过request对象获取请求转发器对象：RequestDispatcher getRequestDispatcher(String path)
         2. 使用RequestDispatcher对象来进行转发：forward(ServletRequest request, ServletResponse response) 
      2. 特点：
         1. 浏览器地址栏路径不发生变化
         2. 只能转发到当前服务器内部资源中
         3. 转发是一次请求
   
   3. ==共享数据==：
   
      * 域对象：一个有作用范围的对象，可以在范围内共享数据
      * **request域**：代表一次请求的范围，一般用于**请求转发的多个资源**中**共享数据**
      * 方法：
        1. **void setAttribute(String name, Object obj)**：存储数据
        2. **Object getAttitude(String name)**：通过键获取值
        3. **void removeAttribute(String name)**：通过键移除键值对
   
   4. ==获取ServletContext==：
   
      * ServletContext getServletContext()



---

## 案例：用户登录

* 用户登录案例需求：

  1. 编写login.html登录页面

     ​	username & password 两个输入框

  2. 使用Druid数据库连接池技术,操作mysql，数据库 day14 中的 user 表

  3. 使用JdbcTemplate技术封装JDBC     

  4. 登录成功跳转到SuccessServlet展示：登录成功！用户名,欢迎您

  5. 登录失败跳转到FailServlet展示：登录失败，用户名或密码错误

* 分析

  * login.html

    * 用户名输入框
    * 密码输入框
    * 登录按钮

  * LoginServlet

    1. 设置字符编码【不清楚中文还是英文，所以统一设置】

    2. 获取 username 和 password

    3. 将 username 和 password 封装为一个User对象

    4. 调用 UserDao 的 login 方法查询，获取返回值 User 对象

    5. 判断 User 对象是否为 null

       * 是：登录失败 -- > 跳转到 FailServlet 展示“登陆失败，用户名或密码错误。 ”

       * 否：登录成功 -- > 先将用户信息存入 request 域 

         ​							-- > 再跳转到 FailServlet 展示

  * UserDao【DAO：Database A操作数据库的类】

    > DAO全称Data Access Object,意思是数据访问对象.在java服务器开发的三层架构中分成控制层(Controller),表示层(service),数据访问层(dao),数据访问层专门负责跟数据库进行数据交互.

* 开发步骤

  1. 创建项目，导入html页面，配置文件，jar包

     > 注：使用MySQL8.0.X版本，MySQL connector **驱动**版本为 mysql-connector-java-8.0.26 。项目的**配置文件**内容如下。

     jdbc.properties

     ~~~properties
     url = jdbc:mysql://localhost:3306/db3
     user = root
     password = 123456
     driver = com.mysql.cj.jdbc.Driver
     ~~~

     druid.properties

     ~~~properties
     driverClassName=com.mysql.cj.jdbc.Driver
     url=jdbc:mysql://localhost:3306/day14
     username=root
     password=123456
     initialSize=5
     maxActive=10
     maxWait=3000
     
     #以下为解决报错添加内容
     filters=stat
     timeBetweenEvictionRunsMillis=60000
     minEvictableIdleTimeMillis=300000
     validationQuery=SELECT 1
     testWhileIdle=true
     testOnBorrow=false
     testOnReturn=false
     poolPreparedStatements=false
     maxPoolPreparedStatementPerConnectionSize=200
     ~~~

     

  2. 创建数据库环境

  ~~~mysql
  CREATE DATABASE day14;
  USE day14;
  CREATE TABLE user(
      id INT PRIMARY KEY AUTO_INCREMENT,
      username VARCHAR(32) UNIQUE NOT NULL,
      password VARCHAR(32) NOT NULL
  );
  ~~~

  3. 创建包 com.anthony.day14_test.domain，创建**实体类** User

  4. 创建包 com.anthony.day14_test.util，编写**工具类** JDBCUtils

     ~~~java
     package com.anthony.day14_test.util;
     
     import com.alibaba.druid.pool.DruidDataSourceFactory;
     
     import javax.sql.DataSource;
     import java.io.IOException;
     import java.io.InputStream;
     import java.sql.Connection;
     import java.sql.SQLException;
     import java.util.Properties;
     
     /**
      * JDBC工具类，使用Druid连接池
      */
     public class JDBCUtils {
         private static DataSource dataSource;
     
         /**
          * 使用工厂-静态代码块加载配置文件、初始化连接池对象
          */
         static {
             try {
                 // 1.加载配置文件
                 Properties properties = new Properties();
                 // 2.使用ClassLoader加载配置文件，获取字节输入流
                 InputStream resourceInputStream = JDBCUtils.class.getClassLoader().getResourceAsStream("druid.properties");
                 properties.load(resourceInputStream);
     
                 // 3.初始化连接池对象
                 dataSource = DruidDataSourceFactory.createDataSource(properties);
             } catch (IOException e) {
                 e.printStackTrace();
             } catch (Exception e) {
                 e.printStackTrace();
             }
         }
     
         /**
          * 获取连接池对象
          */
         public static DataSource getDataSource() {
             return dataSource;
         }
     
         /**
          * 获取链接Connection对象
          */
         public static Connection getConnection() throws SQLException {
             return dataSource.getConnection();
         }
     }
     ~~~

     

  5. 创建包 com.anthony.day14_test.dao，编写**数据库访问类** UserDao

     ~~~java
     package com.anthony.day14_test.dao;
     
     import com.anthony.day14_test.domain.User;
     import com.anthony.day14_test.util.JDBCUtils;
     import org.springframework.dao.DataAccessException;
     import org.springframework.jdbc.core.BeanPropertyRowMapper;
     import org.springframework.jdbc.core.JdbcTemplate;
     
     /**
      * 操作数数据库中 user 表的类
      */
     public class UserDao {
         // 声明JDBCTemplate对象复用
         private JdbcTemplate jdbcTemplate = new JdbcTemplate(JDBCUtils.getDataSource());
     
         /**
          * 登录方法
          * @param loginUser 包含用户从login页面输入的用户名和密码
          * @return 有用户的注册数据，则返回包含所有用户信息的对象；没有查询到返回null
          */
         public User login(User loginUser) {
             try {
                 // 1.编写sql
                 String sql = "SELECT * FROM user WHERE username = ? and password = ?";
                 // 2.调用query方法【利用反射对User对象的属性进行赋值】
                 User queryUser = jdbcTemplate.queryForObject(sql, new BeanPropertyRowMapper<User>(User.class),
                         loginUser.getUsername(), loginUser.getPassword());
                 return queryUser;
             } catch (DataAccessException e) {
                 e.printStackTrace();
                 return null;
             }
         }
     }
     ~~~

     

  6. 创建包 com.anthony.day14_test.web.servlet，编写 LoginServlet

     ~~~java
     package com.anthony.day14_test.web.servlet;
     
     import com.anthony.day14_test.dao.UserDao;
     import com.anthony.day14_test.domain.User;
     
     import javax.servlet.*;
     import javax.servlet.http.*;
     import javax.servlet.annotation.*;
     import java.io.IOException;
     
     @WebServlet("/loginServlet")
     public class LoginServlet extends HttpServlet {
         @Override
         protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
             // 1.设置编码
             request.setCharacterEncoding("utf-8");
             // 2.设置字符编码【不清楚中文还是英文，所以统一设置】
             String username = request.getParameter("username");
             String password = request.getParameter("password");
             // 3.将 username 和 password 封装为一个User对象
             User loginUser = new User();
             loginUser.setUsername(username);
             loginUser.setPassword(password);
             // 4.调用 UserDao 的 login 方法查询，获取返回值 User 对象
             UserDao userDao = new UserDao();
             User queryUser = userDao.login(loginUser);
             // 5.判断 User 对象是否为 null
             if (queryUser == null) {
                 // 登录失败
                 request.getRequestDispatcher("/failServlet").forward(request, response);
             } else {
                 // 登录成功
                 // 存储数据
                 request.setAttribute("user", queryUser);
                 // 转发
                 request.getRequestDispatcher("/successServlet").forward(request, response);
             }
         }
     
         @Override
         protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
             this.doGet(request, response);
         }
     }
     ~~~

     

  7. **注意**：login.html 中 form 表单的 action 路径的写法

     * 虚拟目录 + Servlet 的资源路径

     ~~~html
         <form action="/day14_test/loginServlet" method="post">
             用户名:<input type="text" name="username"> <br>
             密码:<input type="password" name="password"><br>
     
             <input type="submit" value="登录">
     
         </form>
     ~~~

  8. 使用BeanUtils工具类，简化数据封装

     * 问题来源：在 LoginServlet.java 中，假设需要对用户输入的 n 个信息进行校验，则接收用户提交的login.html登录信息并封装为对象需要调用 **n** 次 request.getParameter(), 创建 loginUser 对象，再调用 **n** 次 request.setParameter() 设置对象的属性值。代码大量重复。

     * 解决办法：使用 BeanUtils，通过一个方法获取所有参数，并通过一个方法一次将数据封装为一个对象 

     * BeanUtils相关介绍

       1. **JavaBean**：标准的Java类
          1. 要求：
              1. 类必须被 public 修饰
              2. 必须提供空参构造器
              3. 成员变量必须使用 private 修饰
              4. 提供 setters 和 getters
       2. 概念：
          1. 成员变量
          2. 属性：setter 和 getter 方法截取后的
       3. 方法：
          1. **setProperty()**：设置属性值
          2. **getProperty()**：获取属性值
          3. ==**populate(Object obj, Map, map)**==：将Map集合键值对信息封装到对应的JavaBean对象中。

       

