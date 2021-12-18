# Listener

概念：Listener表示监听器，是JavaWeb三大组件（Servlet、Filter、Listener）之一。

监听器可以监听 application, session, request 三个对象创建、销毁、或者其属性变化情况。

监听器一旦监听到了对象的事件触发，就会自动执行一些代码。





## Listener 分类

JavaWeb中提供了8个监听器

| 监听器分类         | 监听器名称                        | 作用                                           |
| ------------------ | --------------------------------- | ---------------------------------------------- |
| ServletContext监听 | `ServletContextListener`          | 用于对ServletContext对象进行监听（创建、销毁） |
|                    | `ServletContextAttributeListener` | 对ServletContext对象中属性的监听（增删改属性） |
| Session监听        | `HttpSessionListener`             | 对Session对象的整体状态的监听（创建、销毁）    |
|                    | `HttpSessionAttributeListener`    | 对Session对象中属性的监听（增删改属性）        |
|                    | `HttpSessionBindingListener`      | 监听对象于Session的绑定和接触                  |
|                    | `HttpSessionActivationListener`   | 对Session数据的钝化和活化的监听                |
| Request监听        | `ServletRequestListener`          | 对Request对象进行监听（创建、销毁）            |
|                    | `ServletRequestAttributeListener` | 对Request对象中属性的监听（增删改属性）        |





## Listener 基本使用

步骤：

1. 定义类，实现Listener接口
2. 在类上添加`@WebListener`注解



例：`ServletContextListener` 使用

1. 定义`ServletContextListener`类，实现接口

~~~java
package com.anthonycj.web.listener;

import javax.servlet.ServletContextEvent;
import javax.servlet.ServletContextListener;
import javax.servlet.annotation.WebListener;

@WebListener
public class ContextLoaderListener implements ServletContextListener {
    @Override
    public void contextInitialized(ServletContextEvent servletContextEvent) {
        // 加载资源
        System.out.println("ContextLoaderListener...");
    }

    @Override
    public void contextDestroyed(ServletContextEvent servletContextEvent) {
        // 释放资源
    }
}
~~~

