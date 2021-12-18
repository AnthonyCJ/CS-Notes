# JSON

* 概念：JavaScript Object Notation。JavaScript 对象表示法
* 由于其语法简单，层次结构鲜明，现多用于作为<font color="ff9f44">数据载体</font>，在网络中进行数据传输



## 1. JSON基础语法

* 定义：

~~~json
var 变量名 = {
    "key1":value1,
    "key2":value2,
    ...
};
~~~

* 示例

~~~json
var json = {
    "name":"anthony",
    "age":22,
    "addr":["北京","上海","深圳"]
};
~~~

* value的数据类型类型为：
  * 数字（整数或浮点数）
  * 字符串（在双引号中）
  * 逻辑值（true或false）
  * 数组（在方括号中）
  * 对象（在花括号中）
  * null

* 获取数据：

~~~json
变量名.key
~~~

~~~json
json.name	// 示例
~~~







## 2. JSON数据和Java对象转换

* `Fastjson`是阿里巴巴提供的一个Java语言编写的高性能功能完善的JSON库，是目前Java语言中速度最快的JSON库，可以实现Java对象和JSON字符串的相互转换。

使用方法：

1. 导入坐标

~~~xml
<!--JSON-->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>fastjson</artifactId>
    <version>1.2.62</version>
</dependency>
~~~

2. Java对象转JSON

~~~java
String jsonStr = JSON.toJSONString(obj);
~~~

3. JSON字符串转Java对象

~~~java
User user = JSON.parseObject(jsonStr,User.class); 
~~~







## 3. 案例

* 需求：使用Axios+JSON完成品牌列表数据查询和添加



