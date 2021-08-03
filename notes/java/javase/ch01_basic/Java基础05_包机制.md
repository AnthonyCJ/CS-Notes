# Java基础_包

---

​                                                                                                                                                                                 

Java 允许使用包（package）将类组织在一个集合中。借助包可以方便地组织自己的代码，并将自己的代码与别人提供的代码库分开管理。

## 包名

为了保证包名的绝对唯一性，要用一个==因特网域名==（这显然是唯一的）以==逆序形式==作为包名，然后对于不同的工程使用不同的子包。

以百度的域名为例

* com.baidu
  * com.baidu.baike

以个人为例

* com.anthony.blog
  * com.anthony.blog.java
    * com.anthony.blog.java.base
    * com.anthony.blog.java.operator



## 类的导入

* 格式

~~~java
import package1.package2.class1;
~~~



