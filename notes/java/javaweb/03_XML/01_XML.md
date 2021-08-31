# XML

---

## 目录

1. 概念
2. 语法
3. 解析





## 1. 概念

* 概念：**Extensible Markup Language** **可扩展标记语言**
  * 可扩展：标签都是自定义的。 <user>  <student>
  * 功能
    * 存储数据
      1. **作为配置文件**
      2. **在网络中传输**
  *  xml与html的区别
    1. **XML**标签都是**自定义**的，**HTML**标签是**预定义**。
    2. **XML**的语法**严格**，HTML语法松散
    3. **XML**用于存储数据的，**HTML**用于**展示数据**
  * w3c:万维网联盟





## 2. 语法

### 2.1 基本语法

1. XML文档的后缀名 .xml
2. **XML第一行**必须定义为**文档声明**
3. XML文档中**有且仅有一个根标签**
4. 属性值必须使用引号(单双都可)引起来
5. 标签必须正确关闭
6. xml标签名称区分大小写

### 2.2 快速入门

~~~xml
<?xml version='1.0' encoding='gbk'?>
<users>
    <user id='1'>
        <name>zhangsan</name>
        <age>23</age>
        <gender>male</gender>
        <br/>
    </user>

    <user id='2'>
        <name>lisi</name>
        <age>24</age>
        <gender>female</gender>
    </user>
</users>
~~~

### 2.3 组成部分

1. 文档声明
   1. 格式：<?xml 属性列表 ?>
   2. 属性列表：
      * **version**：版本号。**必需的属性**
      * **encoding**：编码方式。告知解析引擎当前文档使用的字符集，默认值：ISO-8859-1
      * standalone：是否独立
        * 取值：
          * yes：不依赖其他文件
          * no：依赖其他文件
2. 指令(了解)：结合css的
   * <?xml-stylesheet type="text/css" href="a.css" ?>
3. 标签：标签名称自定义的
   * 规则：
     * 名称可以包含字母、数字以及其他的字符 
     * 名称不能以数字或者标点符号开始 
     * 名称不能以字母 xml（或者 XML、Xml 等等）开始 
     * 名称不能包含空格 
4. 属性
   1. id属性值唯一
5. 文本：
   * CDATA区：在该区域中的数据会被原样展示
     * 格式：  <![CDATA[ 数据 ]]>

### 2.4 约束：规定xml文档的书写规则

* 作为框架的使用者(程序员)：

  1. 能够在xml中引入约束文档
  2. 能够简单的读懂约束文档

* 分类：

  1. DTD:一种简单的约束技术
  2. Schema:一种复杂的约束技术

* DTD：

  * 引入dtd文档到xml文档中
    * 内部dtd：将约束规则定义在xml文档中
    * 外部dtd：将约束的规则定义在外部的dtd文件中
      * 本地：<!DOCTYPE 根标签名 SYSTEM "dtd文件的位置">
      * 网络：<!DOCTYPE 根标签名 PUBLIC "dtd文件名字" "dtd文件的位置URL">

* Schema:

  * 引入：

    1. 填写xml文档的根元素

    2. 引入xsi前缀.  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"

    3. 引入xsd文件命名空间.  xsi:schemaLocation="http://www.itcast.cn/xml  student.xsd"

    4. 为每一个xsd约束声明一个前缀,作为标识  xmlns="http://www.itcast.cn/xml" 

       > http://www.itcast.cn/xml 是用来标识命名空间的网络地址
       >
       > xmlns后什么都不写表示默认的前缀；
       >
       > xmins:a 表示命名空间为a，使用方法——<a:table...>

    ~~~xml
    <students   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
              xmlns="http://www.itcast.cn/xml"
              xsi:schemaLocation="http://www.itcast.cn/xml  student.xsd">
    ~~~

    > **xmlns** 是 xml namespace 的缩写，也就是**XML命名空间**，xmlns 属性可以在文档中定义一个或多个可供选择的命名空间。该属性可以放置在文档内任何元素的开始标签中。该属性的值类似于 URL，它定义了一个命名空间，浏览器会将此命名空间用于该属性所在元素内的所有内容。
    >
    > ---
    >
    > 在xhtml中，允许你使用各个不同的DTD文件，有可能不同的DTD文件中包含了相同的标识，那么这样的话就会出现标志冲突的问题，如果使用xmlns的话就可以区别开在不同文件中的相同的标志，例如在a.dtd中和b.dtd中都包含了<table>，但是如果一个xhtml文件同时包含了这两个dtd文件，而且使用了标志<table>，这样就会出现标志冲突问题，为了解决标志冲突问题，就可以使用xmlns如下：
    >
    > ~~~xml
    > <html xmlns:a="http://www.a.com">
    > <html xmlns:b="http://www.b.com">
    > <a:table...>
    > <b:table...>
    > ~~~
    >
    > 这样就可以区分开了使用的是哪个dtd文件中的标志。
    >
    > ---
    >
    > 附：根据定义：==**用来标识命名空间的网络地址并不被XML解析器调用，XML解析器不需要从这个网络地址中查找信息，该网络地址的作用仅仅是给命名空间一个唯一的名字，因此这个网络地址也可以是虚拟的**==
    >
    > ==**那么对于浏览器来说，如何知道<a:table...>应该是哪个dtd文件中的标志呢？个人理解应该是在每个xml的dtd文件中都会规定xmlns这个值，如果dtd文件中的xmlns的值与xhtml中的值相等，则认为是匹配的。**==

    

    





## 3. 解析：操作xml文档，将文档中的数据读取到内存中

### 3.1 操作XML文档

1. 解析(读取)：将文档中的数据读取到内存中
2. 写入：将内存中的数据保存到xml文档中。持久化的存储

### 3.2 解析xml的方式

1. DOM（Document Object Model）：将标记语言文档一次性加载进内存，在内存中形成一颗dom树
   * 优点：操作方便，可以对文档进行CRUD的所有操作
   * 缺点：占内存【DOM生成的dom树所占内存大小可能是原文件的上千倍】
   * 通常在服务端【服务器】使用DOM
2. SAX（simple API for XML）：逐行读取，基于**事件驱动**的。
   * 优点：不占内存。
   * 缺点：只能读取，不能增删改。
   * 通常在移动端【Android设备】使用DOM

### 3.3 xml常见的解析器

1. JAXP：sun公司提供的解析器，支持dom和sax两种思想
2. DOM4J：一款非常优秀的解析器
3. Jsoup：jsoup 是一款Java 的HTML解析器，可直接解析某个URL地址、HTML文本内容。它提供了一套非常省力的API，可通过DOM，CSS以及类似于jQuery的操作方法来取出和操作数据。
4. PULL：Android操作系统内置的解析器，是sax方式的。

### 3.4 Jsoup

* Jsoup：jsoup 是一款Java 的HTML解析器，可直接**解析**某个**URL地址、HTML文本内容**。它提供了一套非常省力的API，可**通过DOM，CSS**以及类似于JQuery的操作方法来**取出**和**操作数据**。

* 快速入门：

  * 步骤：
    1. 导入jar包
    2. 获取Document对象【代表整个DOM的树形结构】
    3. 获取对应的标签Element对象 document.getElementById()
    4. 获取数据
  * 代码

  ~~~java
  package com.anthony.javaweb.day12_xml.jsoup;
  
  import org.jsoup.Jsoup;
  import org.jsoup.nodes.Document;
  import org.jsoup.nodes.Element;
  import org.jsoup.select.Elements;
  
  import java.io.File;
  import java.io.IOException;
  
  /**
   * Jsoup快速入门
   */
  public class JsoupDemo1 {
      public static void main(String[] args) throws IOException {
          //2.获取Document对象，根据xml文档获取
          //2.1获取student.xml的path
          /*
              .class获得字节码文件
              .getClassLoader()获取类加载器
              .getResource("name")通过给出的“name”找到资源位置，returns URL object
              .getPath()获取以字符串形式表示的路径，returns String
              注：如果路径有中文或空格，改成.toURI().getPath()
           */
          String path = JsoupDemo1.class.getClassLoader().getResource("student.xml").getPath();
          //2.2解析xml文档，加载文档进内存，获取dom树--->Document对象
          Document document = Jsoup.parse(new File(path), "utf-8");
          //3.获取元素对象 Element
          Elements elements = document.getElementsByTag("name");
          //查看元素个数
          System.out.println(elements.size());
          //3.1获取第一个name的Element对象
          Element firstElement = elements.get(0);
          //3.2获取数据
          String name = firstElement.text();
          System.out.println(name);
      }
  }
  ~~~

### 3.5 对象的使用

1. **Jsoup**：工具类，可以**解析html或xml文档**，**返回Document**
   * parse：解析html或xml文档，返回Document
     * parse(File in, String charsetName)：解析xml或html文件的。
     * parse(String html)：解析xml或html字符串
     * parse(URL url, int timeoutMillis)：通过网络路径获取指定的html或xml的文档对象
2. **Document**：文档对象。代表内存中的dom树
   * 获取Element对象
     * getElementById(String id)：根据id属性值获取唯一的element对象
     * getElementsByTag(String tagName)：根据标签名称获取元素对象集合
     * getElementsByAttribute(String key)：根据属性名称获取元素对象集合
     * getElementsByAttributeValue(String key, String value)：根据对应的属性名和属性值获取元素对象集合
3. **Elements**：元素Element对象的集合。可以当做 ArrayList<Element>来使用
4. Element：元素对象
   1. 获取子元素对象
      * getElementById(String id)：根据id属性值获取唯一的element对象
      * getElementsByTag(String tagName)：根据标签名称获取元素对象集合
      * getElementsByAttribute(String key)：根据属性名称获取元素对象集合
      * getElementsByAttributeValue(String key, String value)：根据对应的属性名和属性值获取元素对象集合
   2. 获取属性值
      * String attr(String key)：根据属性名称获取属性值
   3. 获取文本内容
      * String text():获取文本内容
      * String html():获取标签体的所有内容(包括字标签的字符串内容)
5. Node：节点对象
   * 是Document和Element的父类

### 3.6 快捷查询方式

1. selector:选择器

   * 使用的方法：Elements	select(String cssQuery)
     * 语法：参考Selector类中定义的语法

2. XPath：XPath即为XML路径语言，它是一种用来确定XML（标准通用标记语言的子集）文档中某部分位置的语言

   * 使用Jsoup的Xpath需要额外导入jar包。
   * 查询w3cshool参考手册，使用xpath的语法完成查询
   * 代码

   ~~~java
   //1.获取student.xml的path
   String path = JsoupDemo6.class.getClassLoader().getResource("student.xml").getPath();
   //2.获取Document对象
   Document document = Jsoup.parse(new File(path), "utf-8");
   
   //3.根据document对象，创建JXDocument对象
   JXDocument jxDocument = new JXDocument(document);
   
   //4.结合xpath语法查询
   //4.1查询所有student标签
   List<JXNode> jxNodes = jxDocument.selN("//student");
   for (JXNode jxNode : jxNodes) {
       System.out.println(jxNode);
   }
   
   System.out.println("--------------------");
   
   //4.2查询所有student标签下的name标签
   List<JXNode> jxNodes2 = jxDocument.selN("//student/name");
   for (JXNode jxNode : jxNodes2) {
       System.out.println(jxNode);
   }
   
   System.out.println("--------------------");
   
   //4.3查询student标签下带有id属性的name标签
   List<JXNode> jxNodes3 = jxDocument.selN("//student/name[@id]");
   for (JXNode jxNode : jxNodes3) {
       System.out.println(jxNode);
   }
   System.out.println("--------------------");
   //4.4查询student标签下带有id属性的name标签 并且id属性值为itcast
   
   List<JXNode> jxNodes4 = jxDocument.selN("//student/name[@id='itcast']");
   for (JXNode jxNode : jxNodes4) {
       System.out.println(jxNode);
   }
   ~~~

   

