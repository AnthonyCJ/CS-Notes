## 1. 快速入门

1.新建文本文件，后缀名改为 .html/.htm

2.编写 HTML结构标签

3.在<body>中使用<h1>定义标题字体

4.使用<img>标签定义图片

5.保存后，使用浏览器打开该文件



| 标签      | 描述               |
| :-------- | :----------------- |
| `<HTML>`  | 定义 HTML 文档     |
| `<head>`  | 定义关于文档的信息 |
| `<title>` | 定义文档的标题     |
| `<body>`  | 定义文档的主体     |






## 2. 基础标签

| 标签          | 描述                               |
| :------------ | :--------------------------------- |
| `<h1> ~ <h6>` | 定义标题，h1最大，h6最小           |
| `<font>`      | 定义文本的字体、字体尺寸、字体颜色 |
| `<b>`         | 定义粗体文本                       |
| `<i>`         | 定义斜体文本                       |
| `<u>`         | 定义文本下划线                     |
| `<center>`    | 定义文本居中                       |
| `<p>`         | 定义段落                           |
| `<br>`        | 定义折行                           |
| `<hr>`        | 定义水平线                         |

| HTML 原代码 | 显示结果 | 描述                   |
| ----------- | -------- | ---------------------- |
| `&lt;`      | <        | 小于号或显示标记       |
| `&gt;`      | >        | 大于号或显示标记       |
| `&amp;`     | &        | 可用于显示其它特殊字符 |
| `&quot;`    | “        | 引号                   |
| `&reg;`     | ®        | 已注册                 |
| `&copy;`    | ©        | 版权                   |
| `&trade;`   | ™        | 商标                   |
| `&nbsp;`    |          | 不断行的空白           |



## 3. 进阶标签



### 3.1 图片、音频、视频标签

| 标签      | 描述     |
| --------- | -------- |
| `<img>`   | 定义图片 |
| `<audio>` | 定义音频 |
| `<video>` | 定义视频 |

- >img：定义图片
  
  - src：规定显示图像的 URL
  - height：定义图像的高度
  - width：定义图像的宽度
  
- audio：定义音频。支持的音频格式：MP3、WAV、OGG 
  - src：规定音频的 URL
  - controls：显示播放控件

- video：定义视频。支持的音频格式：MP4, WebM、OGG
  - src：规定视频的 URL
  - controls：显示播放控件



###  3.2 超链接标签

| 标签  | 描述                             |
| ----- | -------------------------------- |
| `<a>` | 定义超链接，用于链接到另一个资源 |

* `href`：指定访问资源的URL
* `target`：指定打开资源的方式
    * `_self`:默认值，在当前页面打开
    * `_blank`：在空白页面打开





###  3.3 列表标签

| 标签   | 描述         |
| ------ | ------------ |
| `<ol>` | 定义有序列表 |
| `<ul>` | 定义无序列表 |
| `<li>` | 定义列表项   |

* type：设置符号的类型
  * `type="1"`：列表标号为1、2、3 ...
  * `type="A"`：列表标号为A、B、C ...






###  3.4 表格标签

| 标签          | 描述           |
| ------------- | -------------- |
| &lt;table&gt; | 定义表格       |
| &lt;tr&gt;    | 定义行         |
| &lt;td&gt;    | 定义单元格     |
| &lt;th&gt;    | 定义表头单元格 |

* table：设置符号的类型



* `<table>`：定义表格
  * `width`：宽度
  * `border`：边框
  * `cellpadding`：定义内容和单元格的距离
  * `cellspacing`：定义单元格之间的距离。如果指定为0，则单元格的线会合为一条、
  * `bgcolor`：背景色
  * `align`：对齐方式
* `<tr>`：定义行
  * `bgcolor`：背景色
  * `align`：对齐方式
* `<td>`：定义单元格
  * `colspan`：合并列
  * `rowspan`：合并行
  * `th`：定义表头单元格
  * `<caption>`：表格标题






###  3.5 布局标签

| 标签     | 描述                                                         |
| -------- | ------------------------------------------------------------ |
| `<div>`  | （块级标签）定义 HTML 文档中的一个区域部分，经常与 CSS 一起使用，用来布局网页 |
| `<span>` | （行内标签）用于组合行内元素。                               |





###  3.6 表单标签

| 标签         | 描述                                 |
| ------------ | ------------------------------------ |
| `<form>`     | 定义表单                             |
| `<input>`    | 定义表单项，通过type属性控制输入形式 |
| `<label>`    | 为表单项定义标注                     |
| `<select>`   | 定义下拉列表                         |
| `<option>`   | 定义下拉列表的列表项                 |
| `<textarea>` | 定义文本域                           |

* `<form>`标签有两个属性
  * `action==""`：表单提交路径
    * 若将数据提交到服务端，该属性需要书写服务端的URL
    * 属性值设为 `#` ，表示提交到当前页面查看效果
  * `method==""`：指定表单提交的方式
    * get方式：默认值
      * 请求的提交参数会拼接在URL后面
      * url的长度有限制：4KB，所以请求参数长度有限制
    * post方式
      * 请求参数会在HTTP请求协议的请求体中



#### 3.6.1 `<input>`标签的type取值

| type 取值  | 描述 |
| -------- | ------------------------------------------------------------ |
| text     | 默认值。定义单行的输入字段 |
| password | 定义密码字段                        |
| radio    | 定义单选按钮                                               |
| checkbox | 定义复选框                                                 |
| file     | 定义文件上传按钮                    |
| hidden   | 定义隐藏的输入字段                                         |
| submit   | 定义提交按钮，提交按钮会把表单数据发送到服务器             |
| reset    | 定义重置按钮，重置按钮会清除表单中的所有数据               |
| button |定义可点击按钮                                                 |


