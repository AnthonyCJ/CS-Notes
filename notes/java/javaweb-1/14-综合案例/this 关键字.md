# this 关键字

对于存在继承关系的类，如果超类的方法中出现了`this`关键字，可以这样辨析`this`所指代的实例：

* **<font color="ff9f44">谁调用我（`this`所在的方法），我（`this`指针）代表谁</font>**

---

举例：

三个类的继承关系如下：

`BrandServlet` extends `BaseServlet`, `BaseServlet` extends `HttpServlet`

* BrandServlet 包含对品牌数据相关请求的各类处理方法
* BaseServlet 根据请求的最后一段路径来进行方法分发，重载了 HttpServlet 中的`service()`方法

如果在BaseServlet的service()方法中使用了`this`关键字，而页面将请求数据发送给了了BrandServlet，那么这个`this`指针指代哪一个实例？

答案是：`BrandServlet`

> **解析**	前端页面的访问路径是虚拟路径，发送到Tomcat需要由Servlet进行解析。由于路径指向了BrandServlet，则由BrandServlet进行请求处理。BrandServlet会自动调用其继承的service()方法。由于service()方法是由BrandServlet调用的，所以这里的`this`关键字引用的是BrandServlet。