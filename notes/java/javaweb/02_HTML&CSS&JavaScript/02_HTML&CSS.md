# HTML&CSS

---

## 目录

1. HTML标签：表单标签
2. CSS
3. 案例



---

## 1. HTML标签：表单标签

### 1.1 表单

* 概念：**用于采集用户输入的数据的。用于和服务器进行交互。**
* ==form标签==：**用于定义一个表单。**可以定义一个范围，范围代表采集用户数据的范围
  * 属性：
    * action：指定提交数据的URL
    * method:指定提交方式
      * 分类：一共7种，2种比较常用
        * get：
          1. **请求参数**会在地址栏中显示。**会封装到请求行中**(HTTP协议后讲解)。
          2. 请求参数大小是有限制的。
          3. 不太安全
        * post：
          1. **请求参数**不会再地址栏中显示。**会封装在请求体中**(HTTP协议后讲解)
          2. 请求参数的大小没有限制。
          3. 较安全。
    * ==注意==：表单项中的数据要想被提交：必须指定其name属性
  * 表单项标签：
    * ==**input**==：可以通过type属性值，改变元素展示的样式
      
      * type属性：
        * **text**：文本输入框。【当不指定type值时，**默认为text类型**】
          * placeholder：指定输入框的提示信息，当输入框的内容发生变化，会自动清空提示信息
        
        ~~~html
        <label for="username"> 用户名 </label>：<input type="text" name="username" placeholder="请输入用户名" id="username"><br>
        ~~~
        
        * **password**：密码输入框
        
        ~~~html
        密码：<input type="password" name="password" placeholder="请输入密码"><br>
        ~~~
        
        * **radio**:单选框
          * ==注意==：
            1. 要想让多个单选框实现单选的效果，则多个单选框的name属性值必须一样。
            2. 一般会给每一个单选框提供value属性，指定其被选中后提交的值
            3. checked属性，可以指定默认值【checked="checked"】
        
        ~~~html
        性别：<input type="radio" name="gender" value="male" checked="checked"> 男
        	<input type="radio" name="gender" value="female"> 女 <br>
        ~~~
        
        * **checkbox**：复选框
          * 注意：
            1. 一般会给每一个单选框提供value属性，指定其被选中后提交的值
            2. checked属性，可以指定默认值
        
        ~~~html
        爱好：<input type="checkbox" name="hobby" value="read" checked="checked"> 读书
            <input type="checkbox" name="hobby" value="sports"> 运动
            <input type="checkbox" name="hobby" value="music"> 音乐 <br>
        ~~~
        
        * **file**：文件选择框
        * **hidden**：隐藏域，用于提交一些信息。
        
        ~~~html
        上传文件：<input type="file" name="file"> <br>
        隐藏域：<input type="hidden" name="id" value="a"> <br>
        ~~~
        
        * 按钮：
          * submit：提交按钮。可以提交表单
          * button：普通按钮
          * image：图片提交按钮
            * src 属性指定图片的路径
          
          ~~~html
          <input type="submit" value="登录">
          <input type="button" value="按钮">
          <input type="image" src="img/regbtn.jpg">
          ~~~
        
      * label：指定输入项的文字描述信息
      
          * ==注意==：
            * label的for属性一般会和 input 的 id属性值 对应。如果对应了，则点击label区域，会让input输入框获取焦点。
      
          ~~~html
          <label for="username"> 用户名 </label>：<input type="text" name="username" 			placeholder="请输入用户名" id="username"><br>
          ~~~
      
    * **select**: 下拉列表
      
      * 子元素：option，指定列表项
      
      ~~~html
      省份：<select name="province">
              <option value="">--请选择--</option>
              <option value="1" selected="selected">北京</option>
              <option value="2">上海</option>
              <option value="3">深圳</option>
          </select> <br>
      ~~~
      
    * **textarea**：文本域
      * cols：指定列数，每一行有多少个字符
      * rows：默认多少行。 
      
      ~~~html
      自我描述：<textarea rows="5" cols="20" name="description"></textarea> <br>
      ~~~



---

## 2. CSS：页面美化和布局控制

### 2.1 概念

CSS：Cascading Style Sheets 层叠样式表

* 层叠：多个样式可以作用在同一个html的元素【即标签】上，同时生效

### 2.2 好处

1. 功能强大
2. **将内容展示和样式控制分离**
   * 降低耦合度。解耦
   * 让分工协作更容易
   * 提高开发效率

### 2.3 CSS的使用：CSS与html结合方式

1. **内联样式**

   * 在标签内使用style属性指定css代码z
   * 如：<div style="color:red;">hello css</div>

2. **内部样式【较常用】**

   * 在head标签内，定义style标签，style标签的标签体内容就是css代码

   * 如：

     ~~~css
     <!--a.css文件-->
     <style>
     div{
         color:blue;
     }
     
     </style>
     <div>hello css</div>
     ~~~

3. **外部样式【较常用】**

   1.  定义css资源文件。

   2.  在head标签内，定义link标签，引入外部的资源文件

      * 如：（）

      ~~~css
      * a.css文件
      div {
          color: green;
      }
      其它文件使用方法
      <link rel="stylesheet" href="css/a.css">
      <div>hello css</div>
      ~~~

* 注意：

  * 1,2,3种方式 css作用范围越来越大

  * 1方式不常用，后期常用2,3

  * 3种格式可以写为：

    ~~~css
    <style>
    	@import "css/a.css";
    </style>
    ~~~

### 2.4 css语法：

* 格式：

  ~~~css
  选择器 {
      属性名1:属性值1;
      属性名2:属性值2;
      ...
  }
  ~~~

* 选择器:筛选具有相似特征的元素

* 注意：

  * 每一对属性需要使用；隔开，最后一对属性可以不加；

### 2.5 选择器：筛选具有相似特征的元素

* 分类：
  1. 基础选择器
     1. **id选择器**：选择具体的id属性值的元素.建议在一个html页面中id值唯一
        * 语法：`#id属性值{}`
     2. **元素选择器**：选择具有相同标签名称的元素
        * 语法： 标签名称{}
        * ==注意==：id选择器优先级高于元素选择器
     3. **类选择器**：选择具有相同的class属性值的元素。
        * 语法：.class属性值{}
        * ==注意==：类选择器选择器优先级高于元素选择器
  2. 扩展选择器：
     1. 选择所有元素：
        * 语法： *{}
     2. 并集选择器：
        * 选择器1,选择器2{}
     3. 子选择器：**筛选**选择器1元素下的**选择器2元素**
        * 语法：  选择器1 选择器2{}
     4. 父选择器：**筛选**选择器2的父元素**选择器1**
        * 语法：  选择器1 > 选择器2{}
     5. 属性选择器：选择元素名称，属性名=属性值的元素
        * 语法：  元素名称[属性名="属性值"]{}
     6. 伪类选择器：选择一些元素具有的状态
        * 语法： 元素:状态{}
        * 如：<a>
          * 状态：
            * link：初始化的状态
            * visited：被访问过的状态
            *  active：正在访问状态
            * hover：鼠标悬浮状态

### 2.6 属性

1. 字体、文本
   * font-size：字体大小
   * color：文本颜色
   * text-align：对其方式
   * line-height：行高 
2. 背景
   * background
3. 边框
   * border：设置边框，符合属性
4. 尺寸
   * width：宽度
   * height：高度
5. 盒子模型：控制布局
   * **margin**：外边距
   * **padding**：内边距
     * 默认情况下内边距会影响整个盒子的大小
     * **box-sizing: border-box**;  设置盒子的属性，让width和height就是最终盒子的大小
   * float：浮动
     * left
     * right



---

## 3. 案例

~~~css
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>注册页面</title>
<style>
    * {
        margin: 0px;
        padding: 0px;
        box-sizing: border-box;
    }
    body {
        background: url("img/register_bg.png") no-repeat center;
        padding-top: 25px;
    }

    .rg_layout{
        width: 900px;
        height: 500px;
        border: 8px solid #EEEEEE;
        background-color: white;
        /*让div水平居中*/
        margin: auto;
    }

    .rg_left{
        /*border: 1px solid red;*/
        float: left;
        margin: 15px;
    }
    .rg_left > p:first-child{
        color:#FFD026;
        font-size: 20px;
    }

    .rg_left > p:last-child{
        color:#A6A6A6;
        font-size: 20px;

    }


    .rg_center{
        float: left;
       /* border: 1px solid red;*/

    }

    .rg_right{
        /*border: 1px solid red;*/
        float: right;
        margin: 15px;
    }

    .rg_right > p:first-child{
        font-size: 15px;

    }
    .rg_right p a {
        color:pink;
    }

    .td_left{
        width: 100px;
        text-align: right;
        height: 45px;
    }
    .td_right{
        padding-left: 50px ;
    }

    #username,#password,#email,#name,#phonenumber,#birthday,#verificationcode{
        width: 251px;
        height: 32px;
        border: 1px solid #A6A6A6 ;
        /*设置边框圆角*/
        border-radius: 5px;
        padding-left: 10px;
    }
    #verificationcode{
        width: 110px;
    }

    #img_verification{
        height: 32px;
        vertical-align: middle; /*垂直居中*/
    }

    #btn_sub{
        width: 150px;
        height: 40px;
        background-color: #FFD026;
        border: 1px solid #FFD026 ;
    }

</style>

</head>
<body>

<div class="rg_layout">
    <div class="rg_left">
        <p>新用户注册</p>
        <p>USER REGISTER</p>

    </div>

    <div class="rg_center">
        <div class="rg_form">
            <!--定义表单-->
            <form action="#" method="post">
                <table>
                    <!--第1行 用户名-->
                    <tr>
                        <td class="td_left"><label for="username">用户名</label> </td>
                        <td class="td_right"><input type="text" name="username" placeholder="请输入账号" id="username"></td>
                    </tr>
                    <!--第2行 密码-->
                    <tr>
                        <td class="td_left"><label for="password">密码</label> </td>
                        <td class="td_right"><input type="password" name="password" placeholder="请输入密码" id="password"></td>
                    </tr>
                    <!--第3行 Email-->
                    <tr>
                        <td class="td_left"><label for="email">Email</label> </td>
                        <td class="td_right"><input type="email" name="email" placeholder="请输入Email" id="email"></td>
                    </tr>
                    <!--第4行 姓名-->
                    <tr>
                        <td class="td_left"><label for="name">姓名</label> </td>
                        <td class="td_right"><input type="text" name="name" placeholder="请输入姓名" id="name"></td>
                    </tr>
                    <!--第5行 手机号-->
                    <tr>
                        <td class="td_left"><label for="phonenumber">手机号</label> </td>
                        <td class="td_right"><input type="text" name="phonenumber" placeholder="请输入手机号" id="phonenumber"></td>
                    </tr>
                    <!--第6行 性别-->
                    <tr>
                        <td class="td_left">性别：</td> <td class="td_right"><input type="radio" name="gender" value="male" checked="checked"> 男
                        <input type="radio" name="gender" value="female"> 女 </td>
                    </tr>
                    <!--第7行 出生日期-->
                    <tr>
                        <td class="td_left"><label for="birthday">出生日期</label> </td>
                        <td class="td_right"><input type="date" name="birthday" placeholder="请输入出生如期" id="birthday"></td>
                    </tr>
                    <!--第8行 验证码-->
                    <tr>
                        <td class="td_left"><label for="verificationcode">验证码</label> </td>
                        <td class="td_right"><input type="text" name="verificationcode" placeholder="请输入验证码" id="verificationcode">
                            <img id="img_verification" src="img/verify_code.jpg"> </td>
                    </tr>
                    <!--第9行 提交按钮-->
                    <tr>
                        <td colspan="2" align="center"><input type="submit" id="btn_sub" value="注册"> </td>
                    </tr>
                </table>

            </form>


        </div>

    </div>

    <div class="rg_right">
        <p>已有账号?<a href="#">立即登录</a></p>
    </div>


</div>


</body>
</html>
~~~
