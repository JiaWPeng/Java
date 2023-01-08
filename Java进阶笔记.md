# 进阶第21天（HTML，CSS）

#### HTML

标记语言

##### 结构

```html
<html>
	<head>
		<title></title>
	</head>
	<body>
	</body>
</html>
<!-- 内容写在body标签中 -->
```

| **标签名** | **定义**   | **说明**                                                     |
| ---------- | ---------- | ------------------------------------------------------------ |
| <h1>-<h6>  | 标题标签   | 作为标题使用，根据重要性递减                                 |
| <p></p>    | 段落标签   | 定义段落，可以将网页分成若干段落                             |
| <br/>      | 换行标签   | 强制换行                                                     |
| <hr/>      | 水平线标签 | 定义  HTML 页面中的主题变化（比如话题的转移），并显示为一条水平线。 |
| <b></b>    | 加粗标签   | 定义粗体的文本                                               |
| <i></i>    | 斜体标签   | 定义呈现斜体的文本                                           |
| <u></u>    | 下划线标签 |                                                              |

##### 超链接标签

<a href="跳转目标" target="目标窗口弹出">

| 属性值 | 作用                                                         |
| ------ | ------------------------------------------------------------ |
| src    | 设置的是图片地址，写的是服务器地址（不能使用盘符来访问图片，会暴露全部计算机中的资源） |
| alt    | 如果图片丢失，替代的文字                                     |
| title  | 如果鼠标移动上去，显示的图片图示                             |
| width  | 宽度，如果只指定宽度，另一个按等比例缩放                     |
| height | 高度                                                         |
| href   | 必须属性，用来指定链接到达的地址                             |
| target | 解决在哪里打开：self默认，在当前窗口打开；blank在新的窗口中打开鼠标移动上去，显示提示信息 |

##### 表格标签

```html
<table>
    <tr>表格中的行
    	<td>内容</td>表格中的列（单元格）
        ...
    </tr>
</table>
```

| 属性名      | 属性值              | 描述                                             |
| ----------- | ------------------- | ------------------------------------------------ |
| align       | left、center、right | 规定表格相对周围元素的对齐方式                   |
| border      | 1或“’               | 规定表格单元是否拥有边框，默认为”“，表示没有边框 |
| cellpadding | 像素值              | 规定单元边沿与其内容直接的空白，默认1像素        |
| cellspacing | 像素值              | 规定单元格之间的空白，默认2像素                  |
| width       | 像素值或百分比      | 规定表格的宽度                                   |

##### 列表标签

```html
别表标签可以添加一个type属性（I，A）、

无序列表
<ul>
    <li>列表项1</li>
    <li>列表项2</li>
    <li>列表项3</li>
</ul>
有序列表
<ol type="I"(type="A")>
    <li>列表项1</li>
    <li>列表项2</li>
    <li>列表项3</li>
</ol>


自定义列表
<dl>
	<dt>刘备</dt>
	<dd>关羽</dd>
</dl>
<dl>
	<dt>关注我们</dt>
    <dd>新浪微博</dd>
    <dd>微信公众号</dd>
</dl>
```

##### Form表单

```html
<form action="url地址" method="提交方式" name="表单域名称">
    各种表单控件
</form>
```

| 属性   | 属性值   | 作用                                               |
| ------ | -------- | -------------------------------------------------- |
| action | url地址  | 用于指定接受并处理表单数据的服务器程序的url地址    |
| method | get/post | 用于设置表单数据的提交方式，其取值为get或post      |
| name   | 名称     | 用于指定表单的名称，一区分同一个页面中的多个表单域 |

###### 表单标签

```html
<input type="" name="" value="" checker="checker">
1.name和value是每个表单元素都有的属性，主要给后台人员使用
2.name表单元素的名字，要求单选按钮和复选框要有相同的name值
3.checked属性主要针对对于单选按钮和复选框，主要作用一打开页面，就可以默认选中某个表单元素。

```

| type的属性值 | 描述                                                         |
| ------------ | ------------------------------------------------------------ |
| button       | 定义可点击按钮                                               |
| checkbox     | 定义复选框                                                   |
| file         | 定义输入字段和“浏览”按钮，供上传文件                         |
| hidden       | 定义隐藏的输入字段                                           |
| image        | 定义图像形式的提交字段                                       |
| password     | 定义密码字段。字段中的字符被掩码                             |
| radio        | 定义单选按钮（多选一）                                       |
| reset        | 定义重置按钮，重置按钮会                                     |
| submit       | 定义提交按钮，提交按钮会把表单数据发送到服务器               |
| text         | 定义单行的输入字符，用户可在其中输入文本，默认宽度为20个字符 |

###### 下拉列表

```html
<select>
    <option>选项1</option>
    <option>选项2</option>
    ....
</select>
1.select中至少包括一对option。
2.在中定义selected="selected"时，当前项即为默认选中项。
```



###### 多行文本框

```html
<textarea rows="3" cols="20">
1：通过<textarea>标签可以轻松地多行文本输入框
2：cols=“每行中的字符数“，rows=”显示的行数“，我们在实际开发中不会使用，都是用css来改变大小

```



#### CSS

是用于指定CSS样式的Html标签

```css
选址器{
	属性1;
	....
}
```

##### 引入方式

1. 内部样式表

   ```html
   <style>
       选择器（标签选择器，Id选择器，class选择器，通用选择器）{
           属性1;
           ...
       }
   </style>
      1：<style>标签理论上可以放在HTML文档的任何地方，但一般会放在文档的<head>标签中。
      2：该方式使代码结构清晰，但是并没有实现结构与样式完全分离。
   
   ```

2. 行内样式表

   ```css
   <div style="color:red; font-size:12px;">行内样式表</div>
      1：style其实就是标签的属性。
      2：可以控制当前的标签设置样式。
      3：由于书写繁琐，并没有体现出结构与样式相分离的思想，所以不推荐大量使用，只有对当前元素添加简单样式的时候，可以考虑使用。
   
   ```

3. 外部样式表

   ```html
   <head>
       <link rel="stylesheet" href="CSS文件路径">
   </head>
   引入外部样式表分为两步：:
      1： 新建一个后缀名为.css的样式文件，把所有CSS代码都放入此文件中。
      2：在HTML页面中，使用<link>标签引入这个文件。
   
   ```

   

| **样式表** | **优点**             | **缺点**     | **使用情况** | **控制范围** |
| ---------- | -------------------- | ------------ | ------------ | ------------ |
| 行内样式表 | 书写方便             | 结构样式混写 | 较少         | 控制一个标签 |
| 内部样式表 | 部分结构和样式分离   | 没有彻底分离 | 较多         | 控制一个页面 |
| 外部样式表 | 完全实现结构样式分离 | 需要引入     | 最多         | 控制多个页面 |

##### CSS基础选择器

根据不同的需求把不同的标签选出来

CSS使用font-family属性定义文本的字体系列。
CSS使用font-size属性定义字体大小。
CSS使用font-weight属性设置文本字体的粗细。
CSS使用color)属性设置文本的颜色。
CSS使用border、width、height属性设置标签的边框和宽高。
CSS使用background-color属性设置标签的背景色。

# 进阶第22天（JS）

是一门跨平台、面向对象的脚本语言，来控制网页的行为，它能使网页可交互，编程类语言

#### 引入方式

1. **内部脚本：**将JS代码定义在HTML页面中

   ```html
   <script>
   	alert("hello JS");
   </script>
   在HTML文档中可以在任意地方，放置任意数量的<script></script>
   一般把脚本置于<body>元素的底部，可改善显示速度，因为脚本执行会拖慢显示
   ```

2. **外部脚本：**将JS代码定义在外部JS文件中，然后引入到HTML页面中

   ```html
   <script src="JS路径"></script>
   外部脚本不能包含<script>标签
   <script>标签不能自闭和
   ```

#### JS语法

区分大小写

每行结尾的分号可有可无

注释：同Java

输出语句

```javascript
使用 window.alert() 写入警告框
使用 document.write() 写入 HTML 输出
使用 console.log() 写入浏览器控制台

```

##### 变量

使用var关键字来声明变量

```javascript
var test = 320;
test = "张三";
//JavaScript是一门弱类型语言，变量可以存放不同类型的值
//var定义的是全局变量
//let定义的是局部变量
//const定义的是常量（不变的量）
```

1. **命名规则：**
   - 组成字符可以是任何字母、数字、下划线或美元符号
   - 不能数字开头
2. ECMAScript 6 新增了 const关键字，用来声明一个只读的常量。一旦声明，常量的值就不能改变。 
3. ECMAScript 6 新增了 let 关键字来定义变量。它的用法类似于 var，但是所声明的变量，只在 let 关键字所在的代码块内有效，且不允许重复声明

##### 数据类型

- number：数字（整数、小数、NaN（不是一个数字））
- string：字符、字符串、单双引都可以
- boolean：布尔
- null：对象为空
- undefined：当声明的变量未初始化是，该变量的默认值是undefined
- typeof：可以获得数据类型

**特点：**

在JS中，五种原始数据类型**都可以作为判断条件使用**，

1. **string**：有内容则为true
2. **number**：不是零的数为true
3. **null**：false
4. **boolean**：
5. **undefined**：false

##### 运算符

- 一元运算符：++，--
- 算术运算符：+，-，*，/，%
- 赋值运算符：=，+=，-=…
- 关系运算符：>，<，>=，<=，!=，==，===…
- 逻辑运算符：&&，||，!
- 三元运算符：条件表达式 ? true_value : false_value 

==和===：**==会进行类型转换，===不会进行类型转换**

+和Java一样：遇到字符串是连接，如果字符串**是数值类型**的则可以进行**减乘除**操作（不是数字类型的则不可以）

##### 类型转换

- 其他类型转为数字：
  1. string将字符串字面值转为数字。如果字面值**不是数字**，**则转为NaN**。一般使用**parseInt**方法进行转换（能转的就都转，不能转的为NaN）
  2. boolean：true转为1，false转为0
- 其他类型转为boolean
  1. number：0和NaN转为false，其他true
  2. string：空字符串转为false，其他true
  3. null：转为false
  4. undefined：转为false

##### 流程控制语句

- if:
- switch:
- for:
- while:
- do…while:

##### 函数（方法）

是被设计为执行特定任务的代码块

通过**function**关键字进行定义

```
//方式1
function 函数名(参数1，参数2){
	代码块；
}
//方式2（匿名函数）不能单独使用，可以作为参数使用
var functionName = function (参数列表){
	要执行的代码
}
```

**注：**形式参数不需要类型。因为JavaScript是弱类型语言；返回值也不需要定义类型，可以在函数内部之间是用**return**返回

调用：JS中，**函数调用可以传递任意个数参数**

**调用：**函数名(实际参数列表)，只根据**函数名调用**，**不存在重载**（**直接覆盖上一个重名方法**，瀑布式加载）

```js
//html页面第一次加载会直接运行带括号的方法
pwd.onblur = checkPwd();//这样会直接运行方法
pwd.onblur = checkPwd;                                                        
      function checkPwd(){
          var length = pwd.value.length;
          if (length>0&&length<20){
              pwdErr.style.display="none";
              return true;
          }else{
              pwdErr.style.display=null;
              return false
          }
      }
```



##### JavaScript对象

###### Array

定义

```javascript
//方式1，未定义的元素值为undefined
var 变量名 = new Array(元素列表);
//方式2
var 变量名 = [元素列表];
```

访问：同Java（**数组名[索引] = 值**）

**注：JS数组类似于Java集合，长度，类型都可变**

###### Array属性

| 属性        | 描述                             |
| ----------- | -------------------------------- |
| constructor | 返回对创建此对象的数组函数的引用 |
| length      | 设置和返回数组中元素的数目       |
| prototype   | 使您有能力向对象添加属性和方法   |

###### Array对象方法

| 方法             | 描述                                                         |
| ---------------- | ------------------------------------------------------------ |
| concat()         | 连接两个或更多的数组，并返回结果                             |
| join()           | 把数组的所有元素放入一个字符串。元素通过制定的分割符进行分割 |
| pop()            | 删除并返回数组的最后一个元素                                 |
| push()           | 向数组的末尾添加一个或更多元素，并返回新的长度               |
| reverse()        | 颠倒数组中元素的顺序                                         |
| shift()          | 删除并返回数组的第一个元素                                   |
| sllce()          | 从某个已有的数组返回选定的元素                               |
| sort()           | 对数组的元素进行排序                                         |
| spllce()         | 删除元素，并向数组添加新元素                                 |
| toSource()       | 返回该对象的源代码                                           |
| toString()       | 把数组转换为字符串，并返回结果                               |
| toLocaleString() | 把数组转换为本地数组，并返回结果                             |
| unshift()        | 向数组的开头添加一个或更多元素，并返回新的长度               |
| valueOf()        | 返回数组对象的原始值                                         |

###### String

定义

```js
//方式1
var 变量名 = new String(s);
//方式2
var 变量名 = s;
```

属性：length：字符串的长度

方法：

​	charAt()：返回在指定位置的字符

​	indexOf()：检索字符串

​	其他同Java

###### 自定义对象

格式

```
var 对象名称 = {
	属性名称1:属性值1,
	属性名称2:属性值2,
	...
	函数名称:function(形参列表){},
	...
}
```

##### BOM

Browser Object Model 浏览器对象模型

JavaScript 将浏览器的各个组成部分封装为对象

组成：

###### Window：浏览器窗口对象

获取：直接使用window，其中window，可以省略

```javascript
window.alert("abc");
```

**属性**：获取其他BOM对象

- **history**
  - 对History对象的只读引用。请参数History对象。
- **Navigator**
  - 对Navigator对象的只读引用。请参数Navigator对象。
- **Screen**
  - 对Screen对象的只读引用。请参数Screen对像。
- **location**
  - 用于窗口或框架的Location对象。请参阅Location对象。

**方法**

- **alert()**

  - 显示带有一段消总和一个确认按钮的警告框。

- **confirm()**

  - 显示带有一段消总以及确认按钮和取消按钮的对话框。返回true和false

- **setInterval()**

  - 按照指定的周期（以毫秒计）来调用函数或计算表达式。多少毫秒后重复调用

  - ```
    setInterval(function(){方法体},毫秒值);
    ```

    

- **setTimeout()**

  - 在指定的毫秒数后调用函数或计算表达式。

  - ```js
    setTimeout(function(){方法体},毫秒值);
    ```

    

###### Navigator：浏览器对象

###### Screen：屏幕对象

###### History：历史记录对象

使用window.history获取，window.可以省略

获取：

```
window.history.方法();
history.方法();
```

方法：

- back()
  - 加载history列表中的前一个URL。后退
- forward()
  - 加载history列表中的下一个URL。前进

###### Location：地址栏对象

使用 window.location获取，其中window. 可以省略

获取

```
window.location.方法();
location.方法();
```

属性：

href：设置或返回完整的URL

##### DOM

Document Object Model 文档对象模型

将标记语言的各个组成部分封装为对象

JavaScript **通过 DOM**， 就能够对 **HTML进行操作了**：

1. 改变 HTML 元素的内容
2. 改变 HTML 元素的样式（CSS）
3. 对 HTML DOM 事件作出反应
4. 添加和删除 HTML 元素

###### Document：整个文档对象

###### Element：元素对象

**获取**：

使用Document对象的方法来获取

- getElementById：根据id属性值获取，返回一个Element对象

- getElementsByTagName：根据标签名称获取，返回Element对象数组

- getElementsByName：根据name属性值获取，返回Element对象数组

- getElementsByClassName：根据class属性值获取，返回Element对象数组

###### Attribute：属性对象

###### Text：文本对象

**innerHTML**方法可以修改标签内容

**元素对象名.style.样式名="值**"；

###### Comment：注释对象

##### 事件监听

事件：HTML事件是放生在HTML元素上的，如按钮被点击、鼠标移动到元素之上、按下键盘按键

JavaScript可以在事件被侦测到时执行代码

###### 事件绑定

```javascript
//方式一：通过HTML标签中的事件属性进行绑定
<input type="button" onclick='on()'>
function on(){
	alert("我被点击了")
}
//方式二：通过DOM元素属性绑定
<input type="button" id="but">
document.getElementById("but").onclick = function(){
	alert("我被点击了")
}
```



###### 常见事件

| **事件名**  | **说明**                 |
| ----------- | ------------------------ |
| onclick     | 鼠标单击事件             |
| onblur      | 元素失去焦点             |
| onfocus     | 元素获得焦点             |
| onload      | 某个页面或图像被完成加载 |
| onsubmit    | 当表单提交时触发该事件   |
| onkeydown   | 某个键盘的键被按下       |
| onmouseover | 鼠标被移到某元素之上     |
| onmouseout  | 鼠标从某元素移开         |

# 进阶第23天（Java Web）

HTTP-请求数据格式

```java
GET / HTTP/1.1 
Host: www.itcast.cn 
Connection: keep-alive 
User-Agent: Mozilla/5.0 Chrome/91.0.4472.106
…
POST / HTTP/1.1 //请求行
Host: www.itcast.cn //请求头
Connection: keep-alive 
Cache-Control: max-age=0 Upgrade-Insecure-Requests: 1 
User-Agent: Mozilla/5.0 Chrome/91.0.4472.106

username=superbaby&password=123456//请求体

```

##### 请求规则（格式）

**请求数据分为3部分：**

1. **请求行**：请求数据的第一行。其中GET表示请求方式， / 表示请求资源路径，HTTP/1.1表示协议版本
2. **请求头**：第二行开始，格式Key：Value形式
3. **请求体**：POST请求的最后一部分，存放请求参数

**GET请求和POST请求区别：**

1. GET请求**参数在请求行中**，没有请求体。POST请求参数**在请求体中**

2. **GET**请求**参数大小有限制**，POST没有

   ```html
   请注意，查询字符串（名称/值对）是在 GET 请求的 URL 中发送的：
   /test/demo_form.php?name1=value1&name2=value2
   
   请注意，查询字符串（名称/值对）是在 POST 请求的 HTTP 消息主体中发送的：
   POST /test/demo_form.php HTTP/1.1
   Host: runoob.com
   name1=value1&name2=value2
   ```

   

**常见的HTTP 请求头：**

- Host: 表示请求的主机名
- User-Agent: 浏览器版本，例如Chrome浏览器的标识类似Mozilla/5.0 ... Chrome/79，IE浏览器的标识类似Mozilla/5.0 (Windows NT ...) like Gecko；
- Accept：表示浏览器能接收的资源类型，如text/*，image/*或者*/*表示所有；
- Accept-Language：表示浏览器偏好的语言，服务器可以据此返回不同语言的网页；
- Accept-Encoding：表示浏览器可以支持的压缩类型，例如gzip, deflate等。



**HTTP协议特点：**

**基于TCP协议：**面向连接，安全

基于请求-响应模型的：**一次请求对应一次响应**

HTTP协议是无状态的协议：对于事务处理没有记忆能力。每次请求响应都是独立的。

**缺点：**多次请求间不能共享数据。

**优点**：速度快

##### 响应规则（格式）

**HTTP-响应数据格式**

```java
HTTP/1.1 200 OK//响应行（协议，状态码，状态码描述）
Server: Tengine//响应头
Content-Type: text/html
Transfer-Encoding: chunked…

<html>//响应体
<head>
     <title></title>
</head>
<body></body>
</html>

```

响应数据分为3部分：

1. 响应行：响应数据的第一行。其中HTTP/1.1表示协议版本，200表示响应状态码，OK表示状态码描述
2. 响应头：第二行开始，格式为key：value形式
3. 响应体： 最后一部分。存放响应数据

常见的HTTP 响应头：

- Content-Type：表示该响应内容的类型，例如text/html，image/jpeg；
- Content-Length：表示该响应内容的长度（字节数）；
- Content-Encoding：表示该响应压缩算法，例如gzip；
- Cache-Control：指示客户端应如何缓存，例如max-age=300表示可以最多缓存300秒
- Content-Type：表示该响应内容的类型，例如text/html，image/jpeg；
- Content-Length：表示该响应内容的长度（字节数）；
- Content-Encoding：表示该响应压缩算法，例如gzip；
- Cache-Control：指示客户端应如何缓存，例如max-age=300表示可以最多缓存300秒

| 状态码分类 | 说明                                                         |
| ---------- | ------------------------------------------------------------ |
| 1xx        | 响应中一—临时状态码，表示请求已经接受，告诉客户端应该继续请求或者如果它已经完成测忽略它 |
| 2xx        | 成功一一表示请求已经被成功接收，处理已完诚                   |
| 3xx        | 重定向一一重定向到其它地方：它社客户端再发起一个请求以完成整个处理， |
| 4xx        | 客户端错误一—处理发生错误，责任在客户端，如：客户端的请求一个不存在的资源，客户端未被授权，禁止访问等 |
| 5xx        | 服务器端错误-一处理发生错误，责任在服务端，如：服务端抛出异常，路由出错，HTP版本不支持等 |

#### 创建Java Web项目

1. 先创建Maven项目，在pom.xml中坐标版本后面添加一个<packaging>war</packaging>刷新Maven
2. Ctrl+Shift+Alt+s打开Project Structure选择Facets双击红色目录添加webapp目录结构，再点击Deployment Description的加号添加web.xml文件一定要在webapp目录下
3. 打开Run =》Edit Configurations 点击加号选择TomcatServer =》Local =》Deployment的加号添加当前web项目

#### Servlet

Servlet是Java提供的一门Web资源开发技术

Servlet是JavaEE规范之一，就是一个接口，将来我们需要定义Servlet类实现Servlet接口，并由web服务器运行Servlet

###### Servlet是什么

1. Servlet是一个运行在web服务端的java小程序
2. 它可以用于接收和响应客户端的请求
3. 要想实现Servlet功能，可以实现Servlet接口，继承GenericServlet或者HttpServlet
4. 每次清求都会执行service方法
5. Servleti还支持配置

##### Servlet实现步骤

1. 创建web项目，导入Servlet依赖坐标

   ```
   <dependency>    	<groupId>javax.servlet</groupId>    	<artifactId>javax.servlet-api</artifactId>    	<version>3.1.0</version>  				<scope>provided</scope>
   </dependency>
   ```

2. 创建：定义一个类，实现Servlet接口，并重写接口中所有方法，并在service方法中输入一句话

   ```
   public class ServletDemo1 implements Servlet {
       public void service(){}
   }
   ```

3. 配置：在类上使用@WebServlet注解，配置该Servlet的访问路径

   ```
   @WebServlet("/demo1")
   public class ServletDemo1 implements Servlet 
   ```

4. 访问：启动Tomcat，浏览器输入URL访问该Servlet

   ```
   http://localhost:8080/day09/demo1
   ```

##### Servlet执行流程

1. Servlet有web服务器创建
2. Servlet的方法由web服务器调用（我们自定义的Servlet，必须实现Servlet接口并重写方法，所以服务器指定我们的Servlet一定有方法）

##### Servlet生命周期

Servlet运行在Servlet容器(web服务器)中，其生命周期**由容器**来管理，分为4个阶段：

1. **加载和实例化**：默认情况下，当Servlet第一次被访问时，由容器创建Servlet对象
2. **初始化**：在Servlet实例化之后，容器将调用Servlet的**init()方**法初始化这个对象，完成一些如加载配置文件、创建连接等初始化的工作。该方法只调用一次
3. **请求处理**：**每次请求Servlet**时，Servlet容器都会调用Servlet的**service()**方法对请求进行处理。
4. **服务终止**：当需要释放内存或者容器关闭时，容器就会调用Servlet实例的**destroy()方法完成资源的释放**。在destroy()方法调用之后，容器会释放这个Servlet实例，该实例随后会被Java的垃圾收集器所回收



```java
@WebServlet(urlPatterns = "/demo",loadOnStartup = 1)
//urlPatterns属性就是value属性：代表设置Servlet的虚拟路径
//负整数：第一次被访问时创建Servlet对象
//0或正整数：服务器启动时创建Servlet对象，数字越小优先级越高
```

Servlet方法：

```java
//初始化方法，在Servleti被创建时执行，只执行一次
void init(ServletConfig config)
//提供服务方法（请求），每次Servlet被访问，都会调用该方法
void service(ServletRequest req,ServletResponse res)
//销毁方法，当Servlet被销毁时，调用该方法。在内存释放或服务器关闭时销毁Servlet
void destroy()
//获取ServletConfig对象
ServletConfig getServletConfig(
//获取Servlet信息
String getServletlnfo()
```

##### Servlet体系结构

**HttpServlet**（对HTTP协议封装的Servlet实现类）==》**GenericServlet**（Servlet抽象实现类）==》**Servlet**（Servlet体系根接口）

我们将来开发B/S架构的web项目，都是针对HTTP协议，所以我们自定义Servlet，会**继承HttpServlet**

###### 快捷构建HttpServlet的子类（Servlet）

包名右键 =》new =》Servlet =》改名字 =》将@WebServlet()中的name属性名删除只留下值

HTTP 协议中，GET 和 POST 请求方式的数据格式不一样，将来要想在Servlet中处理请求参数，得在service方法中判断请求方式，并且根据请求方式的不同，分别进行处理：

```java
//获取请求方式
String method req.getMethod();
//判断请求参数，不同请求方式，进行不一样的处理逻辑
if ("GET".equals(method)){
//执行GET请求方式的处理逻辑
doGet(req,resp);
}else if("POST".equals(method)){
//执行P0ST请求方式的处理逻辑
doPost(req,resp);
}
```

##### Servlet urlPattern配置

Servlet想要被访问，必须配置其访问路径（urlPattern）

**一个Servlet，可以配置多个 urlPattern（虚拟路径）：**@WebServlet(urlPatterns = {"/demo1","/demo2"})

urlPattern **配置规则**

1. 精确匹配：@WebServlet("/user/select")

2. 目录匹配：@WebServlet("/user/*")

3. 扩展名匹配：@WebServlet("*.do")

4. 任意匹配：@WebServlet("/")或@WebServlet("/*")

5. **优先级：**

   ​    **精确路径 > 目录路径 > 扩展名路径 > /* > /**

   **/ 和 /* 区别：**

   - 当我们的项目中的Servlet配置了“/”，会覆盖掉tomcat中的DefaultServlet，当其他的 url-pattern都匹配不上时都会走这个Servlet

   - 当我们的项目中配置了“/*”，意味着匹配任意访问路径

##### XML配置方式编写Servlet

Servlet 从3.0版本后开始支持使用注解配置，3.0版本前只支持 XML 配置文件的配置方式

**步骤：**

1. 编写 Servlet类

2. 在 web.xml中配置该Servlet

   ```xml
   <!-- 同@WebServlet() -->
   <servlet>
   	<servlet-name>demo5</servlet-name>
   	<servlet-class>com.itheima.web.servlet.ServletDemo5</servlet-class>
   </servlet>
   <servlet-mapping>
   	<servlet-name>demo5</servlet-name>
   	<url-pattern>/demo5</url-pattern>
   </servlet-mapping>
   ```



# 查找1099端口的进程：netstat -ano | find "1099"

# 进阶24天

#### Request

web服务器收到客户端的http请求，会对每一次请求，分别创建一个用于代表**请求的request对象**和**代表响应的reponse对象**

 **封装了请求相关的信息，要得到浏览器信息，就找HttpServletRequest对象** 。

###### HttpServletRequest是什么

HttpServletRequest对象代表客户端的请求，当客户端通过HTTP协议访问服务器时，HTTP请求中的所有信息都封装在这个对象中。

**用于**：

1. 防盗链;解析请求来源（**防止链接被其他网站盗用**）
2. **请求参数的获取**&各种表单输入项数据的获取;
3. 请求参数的中文乱码问题的解决；**post请求的参数乱码**

##### 使用Request对象的方法

###### 获取请求行

| 方法                                      | 返回值       | 描述                                                      |
| ----------------------------------------- | ------------ | --------------------------------------------------------- |
| <font color='red'>getMethod()</font>      | String       | 获取提交的方式 (GET,POST)                                 |
| getRequestURI()                           | String       | 获取请求的参数,请求服务器路径                             |
| getRequestURL()                           | StringBuffer | 获取请求的参数,请求服务器**完全路径**（**协议IP端口号**） |
| getQueryString()                          | String       | 获取请求行 问号后面的参数 (GET),?后面的所有参数           |
| <font color='red'>getContextPath()</font> | String       | 获取WEB应用名称                                           |
| getServletPath()                          | String       | 获取Servlet访问路径                                       |
| getRemoteAddr()                           | String       | 获取客户端主机ip                                          |

###### 获取请求头

```java
请求头数据格式键值对, k:v
指导性信息,指导服务器
```

| 方法                   | 返回值 | 描述                         |
| ---------------------- | ------ | ---------------------------- |
| getHeader(String name) | String | 获得一个key对应一个value的请 |

```java
//获取指定请求头
        String host = request.getHeader("Host");//服务器主机地址+端口号
        System.out.println("host："+host);
        String header = request.getHeader("User-Agent");
        System.out.println("浏览器的内核信息:"+header);
```



###### 获取请求参数（请求体）

| 方法                               | 返回值               | 描述                                                        |
| ---------------------------------- | -------------------- | ----------------------------------------------------------- |
| getParameter(表单中的name值)       | String               | 获得提交的参数（一个name对应一个value）                     |
| getParameterValues(表单中的name值) | String[]             | 获得提交的参数（一个name对应多个value）                     |
| getParameterMap()                  | Map<String,String[]> | 获得提交的参数，将提交的参数名称和对应值存入到一个Map集合中 |

```java
//Request对象获取请求参数；获得提交的参数（一个name对应一个value）
public String getParameter(表单中的name值): 
//获得提交的参数（一个name对应多个value）
public String[] getParameterValues(表单中的name值):
//获得提交的参数，将提交的参数名称和对应值存入到一个Map集合中
public Map<String,String[]> getParameterMap(): 
	
```

##### 请求参数解决中文乱码

- 当页面提交方式为GET：Tomcat8.0之后已经解决
- 当页面提交方式为POST：通过**request.setCharacterEncoding("UTF-8")**设置编码



#### Response

是把服务器请求的处理结果告知客户端。在B/S架构中，响应（Reponse）就是把结果带回浏览器

###### HttpServletResponse是什么？

代表服务器的响应。可以完成向客户端发送数据、发送响应头、发送响应状态码

##### Response设置响应行

```
设置响应的状态码
- 200 正确
- 302 重定向
- 304 查找本地缓存
- 404 请求资源不存在
- 500 服务器内部错误
response.setStatus(500);
```



##### Response设置响应头

```
HTTP协议的响应头,数据格式键值对 k:v
包含指导性信息,指导客户端
```

| 方法              | 返回值 | 描述             |
| ----------------- | ------ | ---------------- |
| setStatus(int sc) | void   | 设置响应的状态码 |

| 方法                                                        | 返回值 | 描述                       |
| ----------------------------------------------------------- | ------ | -------------------------- |
| setDateHeader(String key,long l)                            | void   | 更改响应头，值为日期类型   |
| setIntHeader(String key,int value)                          | void   | 更改响应头，值为int类型    |
| <font color='red'>setHeader(String key,String value)</font> | void   | 更改响应头，值为String类型 |

**Response设置响应体**

  HTTP的响应体,就是页面的正文部分

###### 字符流向浏览器写数据

| Response中的方法 | 返回值      | 写数据方法 | 描述                                           |
| ---------------- | ----------- | ---------- | ---------------------------------------------- |
| **getWriter**()  | PrintWriter | write()    | 使用字符串数据,没有差别, 输出是整数,查询编码表 |
| **getWriter**()  | PrintWriter | print()    | 无论是什么,原样打印                            |

##### 字节流向浏览器写数据

| Response中的方法  | 返回值       | 写数据方法 | 描述                                             |
| ----------------- | ------------ | ---------- | ------------------------------------------------ |
| getOutputStream() | OutputStream | write()    | 返回字节输出流OutputStream，响应非文本类型的数据 |

##### 重定向

是通过各种方法将各种网络请求重写定个方法转到其他位置。在我们这里就是利用response对象，把浏览器地址重新定向到一个新的地址上（可以是内部资源也可以是外部资源）

可以使用**response.sendRedirect()**方法实现重定向操作

###### 特点

1. 是由浏览器进行跳转的，进行重定向跳转的时候，浏览器的地址会发生变化
2. 是由浏览器完成跳转，可以去访问任何的资源
3. 重定向至少两次请求。两次响应
4. 由响应对象告知浏览器的进行重新定向

​	

# 进阶第25天（JSP）

#### Reques域对象

是从客户端浏览器向服务器发送的一次请求信息的封装。

那么实质上向Request中所保存的数据有效期也是一次请求范围。

**当服务器做出响应后，请求对象就销毁了**，保存在其中的数据就无效了

##### Request域的范围

request只能在一次请求过程中传输数据。请求结束自动销毁

##### Request可以解决什么问题

- 解决处于同**一个请求多个处理模块之间的数据传输**问题
- 具体表现在通过request对象在实现转发时，把**数据通过request对象**带给**其它web**资源处理

##### Request域对象API

| 方法                                  | 返回值 | 描述                  |
| ------------------------------------- | ------ | --------------------- |
| setAttribute(String name, Object obj) | void   | 向Request域中保存数据 |
| getAttribute(String name)             | Object | 从Request域中获取数据 |
| removeAttribute(String name)          | void   | 从Request域中移除数据 |

#### 请求转发

一种在服务器内部的资源跳转方式

实现方式：**req.getRequestDiapatcher("资源B路径").forward(req.resp);**

请求转发资源间**共享数据**：使用Request对象

```java
void setAttribute(String name, Object o)：存储数据到 request域中

Object getAttribute(String name)：根据 key，获取值

void removeAttribute(String name)：根据 key，删除该键值对
```

请求转发**特点**：

- 浏览器地址栏路径不发生变化
- 只能转发到当前服务器的内部资源
- 一次请求，可以在转发的资源键使用request共享数据

##### 请求转发与重定向辨析

相同点：请求转发和重定向都可以解决，在Servlet程序中，一个Servlet需要调用另外的资源对浏览器进行响应

不同点：

1. 实际发生的位置不同，地址栏不同：
   - 转发是由**服务器**进行跳转，在转发的时候，浏览器的地址栏是没有发生变化的
   - 重定向是由**浏览器**进行跳转，进行重定向跳转的时候，浏览器的地址会发生变化
2. 能够访问的资源范围不一样
   - 转发是服务器跳转只能去往**当前web应用的资源**
   - 重定向是浏览器跳转，可以去**往任何的资源**
3. 传递数据的类型不同
   - 转发的request对象可以**传递各种类型的数据包括对象**
   - 重定向只能传递字符串。**两次请求**，不能再多个资源使用request共享数据。
4. 请求的次数不同
   - 转发是一次请求，一次响应
   - 重定向至少请求两次，响应两次

#### JSP

概念：Java Server Pages，Java服务端页面

一种动态的网页技术，其中既可以定义 HTML、JS、CSS等静态内容，还可以定义 Java代码的动态内容

JSP = HTML + Java

**JSP 本质上就是一个 Servlet**：**当JSP第一次**在被访问时，由JSP容器(Tomcat)将其**转换为 Java文件**(Servlet)，在由JSP容器(Tomcat)将其**编译**，**最终对外提供服务**的其实就是这**个字节码文件**

JSP的作用：简化开发，**避免了在Servlet中直接输出HTML标签**



##### 创建

1. 导入JSP坐标

   ```
   <dependency>  	
   	<groupId>javax.servlet.jsp</groupId>    	
   	<artifactId>jsp-api</artifactId>   	
   	<version>2.2</version>
   	<scope>provided</scope>
   </dependency>
   
   ```

2. 创建JSP文件

3. 编写HTML标签和Java代码

##### JSP脚本

分类：

- <%...%>：内容会直接放到_jspService()方法之中

- <%=…%>：内容会放到out.print()中（**直接在HTML页面输出**），作为out.print()的参数

- <%!…%>：内容会放到_jspService()方法之外（**成员变量位置**），被类直接包含

#### EL表达式

Expression Language 表达式语言，用于简化 JSP页面内的Java代码

**主要功能**：获取数据

**语法**：${request对象中存储的Key名}，获取域中存储的key为brands的数据

JavaWeb中的四大域对象：

1. page：当前页面有效
2. request：当前请求有效
3. session：当前会话有效
4. application：当前应用有效

#### JSTL标签

JSP标准标签库(Jsp Standarded Tag Library) ，**使用标签取代JSP页面上的Java代码**

##### 创建

1. 导入坐标

   ```xml
   <dependency>    
       <groupId>jstl</groupId>    
       <artifactId>jstl</artifactId>    
       <version>1.2</version>
   </dependency>
   <dependency>    
       <groupId>taglibs</groupId>    
       <artifactId>standard</artifactId>    
       <version>1.1.2</version>
   </dependency>
   ```

2. 在JSP页面上引入JSTL标签库

   ```
   <%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %> 
   ```

   

3. 使用

   1. ```html
      <c:if>相当于 if 判断
      属性：test，用于定义条件表达式
      <c:if test="${flag == 1}">
      男
      </c:if>
      <c:if test="${flag == 2}">
      女
      </c:if>
      
      ```

   2. ```html
      <c:forEach>：相当于 for 循环
      items：被遍历的容器
      var：遍历产生的临时变量
      varStatus：遍历状态对象
      
      
      <c:forEach items="${brands}" var="brand">    <tr align="center">
      <td>${brand.id}</td>        <td>${brand.brandName}</td>        <td>${brand.companyName}</td>        <td>${brand.description}</td>    
      </tr>
      </c:forEach>
      
      begin：开始数
      end：结束数
      step：步长
      <c:forEach begin="0" end="10" step="1" var="i">
      ${i}
      </c:forEach>
      
      
      ```

      

#### MVC模式

- M：Model，业务模型，处理业务
- V：View，视图，界面展示
- C：Controller，控制器，处理请求，调用模型和视图

#### 三层架构

- 表现层（包名：web/controller）：接收请求，封装数据，调用数据，调用业务逻辑层，响应数据（实现请求的控制，请求发到哪里）
- 业务逻辑层（service）：对业务逻辑进行封装，组合数据访问层层中基本功能，形成复杂的业务逻辑功能
- 数据访问层（dao/mapper）：对数据库的CRUD基本操作

###### 业务步骤分析

1. 当浏览器向服务器发送一个请求时，调用相应的Servlet（表现层）
2. 在Servlet中调用Service（业务逻辑层）的相应方法，返回数据给Servlet（表现层）
3. 在Service中调用Mapper（数据访问层）的相应方法，返回数据给Service（业务逻辑层）

##### 案例Brand

成品在MyBatis/Day07_Brand下

###### 查询所有品牌方法步骤分析

当点击页面查询按钮后  ====》跳转到SelectAllServlet中 ====》doGet方法中调用BrandService中的selectAll方法 ====》然后selectAll方法调用BrandMapper的selectAll方法返回得List集合 ====》最后将数据显示到brand.jsp



# 进阶第26天（Session）

#### 会话跟踪技术

会话：用户打开浏览器，访问web服务器的资源，会话建立，直到有一方断开连接，会话结束。在一次会话中可以包**含多次请求和响应**

会话跟踪：一种维护浏览器**状态**的方法，服务器需要识别多次请求是否来自于**同一浏览器**，以便在同一次会话的**多次请求间共享数据**

HTTP协议是无状态的，每次浏览器向服务器请求时，服务器都会将该请求视为新的请求，因此我们需要**会话跟踪技术来实现会话内数据共享**

**Session完成了多次请求键数据的共享，前提多次请求在一次会话中**

##### 实现方式：

- 客户端会话跟踪技术：Cookie

- 服务端会话跟踪技术：Session

##### Cookie基本使用

客户端会话技术，将数**据保存到客户端**，以后**每次请求**都携**带Cookie数据进行访问**

1. 创建Cookie对象（在**服务器端创建**），设置数据

   ```java
   Cookie cookie = new Cookie("key","value");
   ```

2. 发送Cookie到客户端：使用response对象

   ```
   response.addCookie(cookie)
   ```

3. 获取客户端携带的所有Cookie，使用request对象

   ```
   Cookie[] cookies = request.getCookies()
   ```

4. 遍历数组，获取每一个Cookie对象：for

5. 使用Cookie对象方法获取数据

   ```
   cookie.getName()
   cookie.getValue()
   ```

##### Cookie原理

Cookie的实现是**基于HTTP协议的**

**响应头**：set-cookie

**请求头**：cookie

##### Cookie使用细节

Cookie 存活时间

- **默认情况下**，Cookie 存储在浏览器内存中，**当浏览器关闭，内存释放，则Cookie被销毁**

- **setMaxAge(int seconds)：设置Cookie存活时间**
  - **正数**：将 Cookie写入浏览器所在电脑的硬盘，**持久化存储。到时间自动删除**
  - **负数**：**默认值**，Cookie在当前浏览器内存中，当浏览器关闭，则 Cookie被销毁
  - **零**：删除对应 Cookie

Cookie 存储中文

- Cookie （Tomcat低版本）不能直接存储中文

- 如需要存储，则需要进行转码：URL编码

##### Session基本使用

服务端会话跟踪技术：将数据保存到服务端

JavaEE 提供 **HttpSession接口**，来实现一次会话的**多次请求间数据共享功能**

使用：

1. 获取Session对象

   ```java
   HttpSession session = request.getSession(); 
   ```

2. Session对象功能：

   1. void setAttribute(String name, Object o)：存储数据到 session 域中
   2. Object getAttribute(String name)：根据 key，获取值
   3. void removeAttribute(String name)：根据 key，删除该键值对

##### Session原理

Session是基于Cookie实现的

**通过cookie传递SessionId**（**cookie传的键：jsession**）来确保

**是同一个Session**

```
HttpSession session = req.getSession();
```

##### Session使用细节

Session 钝化、活化：

- 服务器重启后，Session中的数据是否还在？

  - 钝化：在服务器正常关闭后， Tomcat会自动将 Session数据写入硬盘的文件中**（序列化）**
  - 活化：再次启动服务器后，从文件中加载数据到Session中**（反序列化）**

- Seesion 销毁：

  - 默认情况下，无操作，30分钟自动销毁

    ```xml
    <session-config>
    	<session-timeout>30</session-timeout>
    </session-config>
    ```

    

  - 调用 Session对象的 invalidate()方法

##### Cookie和Session区别

Cookie 和 Session 都是来完成一次会话内多次请求间数据共享的

区别：

- 存储位置：Cookie 是将数据存储在客户端，Session 将数据存储在服务端
- 安全性：Cookie 不安全，Session 安全**（通过SessionId来获取服务器保存的Session数据）**
- 数据大小：Cookie 最大3KB，Session 无大小限制
- 存储时间：Cookie 可以长期存储，Session 默认30分钟
- 服务器性能：Cookie 不占服务器资源，Session 占用服务器资源
- **应用场景**：
  - 记住账号密码：Cookie
  - 很早之前的购物车：Cookie
  - 登录之后用户名的显示：Session
  - 验证码：Seesion

# 进阶第26天

#### Filter

表示过滤器，是

作用：可以把对资源的请求拦截下来，从而实现一些特殊的功能

如：权限控制、统一编码处理、敏感字符等

##### Filter的创建

1. 定义类，实现 Filter接口，并重写其所有方法
2. 配置Filter拦截资源的路径：在类上**定义 @WebFilter 注解**
3. 在doFilter方法中输出一句话，并放行

##### Filter的执行流程

执行放行前逻辑 ===》放行 ===》访问资源 ===》执行放行后逻辑

##### Filter使用细节

1. 拦截路径配置
   - Filter 可以根据需求，配置不同的拦截资源路径
     - 栏截具体的资源：/index.isp:只有访问index.jsp时才会被拦截。
     - 目录拦截：/user/*：访问/user下的所有资源，都会被拦截
     - 后缀名拦截：*.jsp：访问后缀名为jsp的资源，都会被拦截
     - 拦截所有：/*：访问所有资源，都会被拦截
2. 过滤器链
   - 一个Web应用，可以配置多个过滤器，这多个过滤器称为过滤器链
   - 注解配置的Filter，**优先级按照过滤器类名**(字符串)的自然排序

##### Filter对象生命周期

在Tomcat启动时创建，Filter对象只创建一次，init方法也只执行一次，在Tomcat关闭时销毁

#### Listener

表示监听器，是JavaWeb三大组件（Servlet、Filter、Listener）之一

监听器可以监听就是在application,session,request三个对象创建、销毁或者往其中添加修改删除属性时自动执行代码的功能组件

##### Listener分类

| 监听器分类                               | 监听器名称                      | 作用                                            |
| ---------------------------------------- | ------------------------------- | ----------------------------------------------- |
| ServletContext监听                       | ServletContextListener          | 用于对ServletContext对象进行监听（创建、销毁）  |
|                                          | ServletContextAttributeListener | 对ServletContexti对象中属性的监听（增删改属性） |
| Session监听                              | HttpSessionListener             | 对Session对象的整体状态的监听（创建、销毁）     |
|                                          | HttpSessionAttributeListener    | 对Session对象中的属性监听（增删改属性）         |
|                                          | HttpSessionBindingListener      | 监听对象于Session的绑定和解除                   |
|                                          | HttpSessionActivationListener   | 对Session数据的钝化和活化的监听）               |
| Request                             监听 | ServletRequestListener          | 对Requesti对象进行监听（创建、销毁）            |
|                                          | ServletRequestAttributeListener | 对Requesti对象中属性的监听（增删改属性          |

###### ServletContextListener 使用

1. 定义类，实现ServletContextListener接口
2. 在类上添加@WebListener 注解

# 进阶第27天（JSON&Ajax）

#### JSON

JSON (JavaScript Object Notation, JS 对象标记) 是一种轻量级的**数据交换格式**。

##### 为什么有JSON？

在JSON出现之前，大家一直用XML来传递数据。因为XML是一种纯文本格式，所以它适合在网络上交换数据。XML本身不算复杂，但是，加上DTD、XSD、XPath、XSLT等一大堆复杂的规范以后，任何正常的软件开发人员碰到XML都会感觉头大了，最后大家发现，即使你努力钻研几个月，也未必搞得清楚XML的规范。于是更简洁清晰的JSON格式应运而生。风靡全球。

##### **JSON**这种格式有什么特点

1. 纯文本形式并具备简洁和清晰的层次结构。
2. JSON具有“自我描述性”，易于人阅读和编写。
3. 具有层级结构，易于机器解析和生成，可以有效地提升网络传输效率。
4. 可以在js中直接解析。

##### JSON的存储数据格式

###### 对象形式存储数据

语法规则: 

1. 对象可以包含多个 **key/value（键/值）对。**
2. key 必须是字符串，value 可以是合法的 JSON 数据类型（字符串, 数字, 对象, 数组, 布尔值或 null）。
3. key 和 value 中使用冒号(:)分割。
4. 每个 key/value 对使用逗号(,)分割。

```xml
<script>
	/*
     * 默认格式 很像js自定义对象 
     */
    var jsonObj = {
    	/*
    	 * "string"=value
    	 */
    	"name":"heima",
    	"url":"http://www.itheima.com"
    }
    var name = jsonObj.name;
</script>
```



###### 数组形式存储数据

语法规则: 

1. JSON 数组在中括号中书写。
2. JSON 中数组value值必须是合法的 JSON 数据类型（字符串, 数字, 对象, 数组, 布尔值或 null）。
3. JSON数组可以使用jQuery语法完成遍历。

```
["value1","value2"]
["heima","http://www.itheima.com"]
```



###### Value的类型

- string
- number
- object
- array
- true
- false
- null

##### 在页面上怎么访问对象的值

可以使用（.）来访问（修改）对象的值

##### 在页面上怎么访问数组中的值

数组名[索引]来访问或修改数组中的值。

同时数组还支持 for-in 方式遍历。

##### 在Java中使用JSON

java本身没有json对象及数组格式。但是支持字符串，而JSON本质就是一串特殊的字符串。

所以我们只需把**JAVA** **对象或集合转换成** **JSON** **格式的字符串****，传递给前端，前端就可以解析了。

同样如何前段传递json字符串给Java，我们只需把JSON格式字符串转换成相应**Java**对象即可。

##### 使用FastJson解析Json

使用

1. 添加fastjson依赖

   ```
   <dependency>
       <groupId>com.alibaba</groupId>
       <artifactId>fastjson</artifactId>
       <version>1.2.75</version>
   </dependency>
   <dependency>
       <groupId>junit</groupId>
       <artifactId>junit</artifactId>
       <version>4.12</version>
   </dependency>
   ```
   
2. 创建测试类

###### API

- public static final String toJSONString(Object object); // 将JavaBean序列化为JSON文本

- public static final <T> T parseObject(String text, Class<T> clazz); // 把JSON文本parse为JavaBean 

- public static final <T> List<T> parseArray(String text, Class<T> clazz); //把JSON文本parse成JavaBean集合 

#### 单例模式

指一个程序，内存中只会创建且仅创建一次对象的设计模式

##### 饿汉式加载

立即加载，不管用不用，对象都提前准备好

**没有线程安全问题**

```java
保证new一次
1:构造私有化-不让new
2:类中设计一个静态公共的方法 - 外界能访问
	返回Single对象
3:保证对像能被加载，只被加载一次
	静态代可块/静太变量
	都是随着类的加载而（执行）加载
	
public class Single{
	//Single.class进入内容的时属
	//会加载静态变量的内容到方法区
	//然后其会执行一次new操作
	private static Single single = new Single();
	private single(){}
	public static Single getInstance(){
		return single;
	}
}
```

##### 懒汉式加载

延迟加载，不用不产生，用的时候才产生

**有线程安全问题**

```java
public class Single{
	private static Single single;
	private single(){}
	public static Single getInstance(){
        // 判断是否需要加锁，提高效率
        if(single == null){
            synchronized(Single.class){
                // 判断是否创建对象
                if(single == null){
					single = new Single();
				}
            }  
        }
		return single;
	}
}
```



#### 前后端有几种交互

浏览器向服务器发送请求的途径有几种？

1、在浏览器地址栏输入url，按回车键发送；

2、HTML页面中，通过超链接标签<a href="http://www.itheima.com">黑马程序员</a>；

3、在JavaScript中，通过location.href="http://www.itheima.com"发送请求；

4、通过form表单发送请求；

###### 上面几种方式发起请求后，如果服务器迟迟不给响应会出现什么效果？

页面假死。上述请求必须是当前一个事件执行完了才能执行后一个事件。------这种请求响应方式称为同步请求。

###### 同步请求的缺点:

在我们之前的开发，每当用户向服务器发送请求，哪怕只是需要更新一点点的局部内容，服务器都会将整个页面进行刷新。

1. 性能会有所降低(一点内容，刷新整个页面！)
2. 用户的操作页面会中断(整个页面被刷新了)

##### 同步请求和异步请求

同步请求：**串行操作**，发送一个请求，需等待响应，期间不能够再次发送请求；

异步请求：并行操作，发送一个请求，无需等待响应，即可再发送请求；

#### Ajax

Ajax就是完成异步请求的一种技术，是一种在无需重新加载整个页面的情况之下的能够更新部分网页的技术。

应用于表单异步校验、搜索框自动联想

1. 编写AjaxServlet，并使用response输出字符串

2. 创建 XMLHttpRequest 对象：用于和服务器交换数据

   ```
   var xmlhttp= new XMLHttpRequest()
   ```

   

3. 向服务器发送请求

   ```
   xmlhttp.open("GET",“url");
   xmlhttp.send();//发送请求
   
   ```

   

4. 获取服务器响应数据

   ```
   xmlhttp.onreadystatechange = function () {
   	if (xmlhttp.readyState == 4 && xmlhttp.status == 200){        
   	alert(xmlhttp.responseText);    
   }}
   
   ```

   

1. 引入 axios 的 js 文件

   ```
   <script src="js/axios-0.18.0.js"></script>
   ```

2. 使用axios 发送请求，并获取响应结果

   ```js
   //GET异步
   axios({// 向哪里发送
   	method:"get",  
   	url:"http://localhost:8080/ajax-demo1/aJAXDemo1?username=zhangsan"
   }).then(function (resp){// 服务器返回结果
   	alert(resp.data);
   })
   
   //POST异步
   axios({
   	method:"post",
   url:"http://localhost:8080/ajaxdemo1/aJAXDemo1",   
   	data:"username=zhangsan"//也可以是Json数据
   }).then(function (resp){
   	alert(resp.data);
   });
   
   ```
   
   

##### Ajax请求方式别名

| **方法名**         | **作用**         |
| ------------------ | ---------------- |
| get(url)           | 发起GET方式请求  |
| post(url,请求参数) | 发起POST方式请求 |

- axios.get(url[,config])
- axios.delete(url[,config])
- axios.head(url[,config])
- axios.options(url[,config])
- axios.post(url[,data[,config]])
- axios.put(url[,data[,config]])
- axios.patch(url[,data[,config]])

```js
//简化GET方法
axios.get("http://localhost:8080/ajax-demo1/aJAXDemo1?username=zhangsan").then(resp=>{
	alert(resp.data);
});
//简化POST方法
axios.get("http://localhost:8080/ajax-demo1/aJAXDemo1","username=zhangsan").then(resp=>{
	alert(resp.data);
});
```



# 进阶第28天（Vue&ElementUI）

Vue.js是一套用于构建用户界面的渐进式javascript框架。Vue (读音 /vjuː/，类似于 view) 。

VUE框架自出现就被人广泛使用得益于它是一个MVVM模型的框架，理解MVVM有利于学习vue.js

**MVVM拆分解释为：将视图 UI 和业务逻辑分开**

**好处：**

**MVVM响应式编程模型，避免直接操作DOM , 降低DOM操作的复杂性。**

##### Vue的使用

1. 创建工程并导入vue.js文件到工程中

2. 创建HTML文件并引入vue.js

   ```
   <script type="text/javascript src="../js/vue.js"></script>
   ```

3. **new Vue()**:Vue 核心对象,每一个 Vue 程序都是从一个 Vue 核心对象开始的。里面是键值对形式数据。

   ```
   选项列表：
           el 选项：用于接收获取到页面中的元素。(根据常用选择器获取)。
                   绑定操作元素范围。视图view.
                   注意:该元素范围外无法使用vue语法。
           data 选项：用于保存当前 Vue 对象中的数据。在视图中声明的变量需要在此处赋值。
                   数据模型声明。Model
                   注意：数据的展示是键值对形式。键可以在页面直接使用。
           methods 选项：用于定义方法。方法可以直接通过对象名调用，this 代表当前 Vue 对象。
                   一般是点击事件触发对应的函数。
   
   
   {{name}} 这是vue作用于页面上的一种语法 ，这种语法叫插值表达式。  就是把data:{}里面的内容显示出来。  
                特点： 无论何时，绑定数据属性发生变化，对应差值处内容会更新。 
   
   ```

   

##### Vue插值表达式语法介绍

获取vue数据模型中的数据，在页面上显示

**格式：{{表达式}}**

使用有没有上限：

1. 插值表达式一般用于标签体中。
2. **该表达式支持JS语法**，可以调用js内置函数（必须有返回值）如：new Date()
3. **表达式必须有返回结果**，例如 1 + 1。没有结果的表达式不允许使用，如：var a = 1 + 1。
4. 可以直接获取**Vue实例中定义的数据或函数**
5. 插值表达式 **不可以**使用**在标签内**的**属性值上 一定注意！**

**注**：插值表达式一般用来获取数据模型中对应的数据，要求**书写在标签体中** ，不可以出现在标签的属性中

##### Vue常用指令

在vue中提供了一些对于页面 + 数据的更为方便的输出,这些操作就叫做指令。

vue系统指令的特点是，他们都是以v-开头，后面跟上不同的指令名称

| 指令名称  | 作用                                                |
| --------- | --------------------------------------------------- |
| V-html    | 把文本解析为HTML代码                                |
| V-bind    | 为HTML标签绑定属性值                                |
| V-if      | 条件性渲染某元素，条件为true是渲染，否则不渲染      |
| V-else    |                                                     |
| V-else-if |                                                     |
| V-show    | 根据条件展示某元素，区别在于切换的是display属性的值 |
| V-for     | 列表渲染，遍历容器的元素或者对象的属性              |
| V-on      | 为HTML标签绑定事件                                  |
| V-model   | 在表单元素上创建双向数据绑定                        |

##### v-text和v-html

是用来**标签显示文本数据**， 我们也可以使用v-text和v-html指令来替代`{{}}`

\- v-text： 将数据输出到元素内部，如果输出的数据有HTML代码，会作为普通文本输出。

\- v-html：将数据输出到元素内部，如果输出的数据有HTML代码，会被渲染。

```
语法：写在需要的标签属性位置。
v-text="数据模型定义的数据"
v-html="数据摸型定义的数据"

v-text  或者 v-html  一般都使用在页面标签，用于显示标签文本或html片段
```

##### b-bind

v-bind 用于将vue的值绑定给对应dom的属性值。

**主要用于对标签的元素属性赋值**

```
语法：
v-bid:元素属性名="数据模型定义的初始数据"
//简化语法
:元素属性名="数据模型定义的初试数据"
```

##### v-model双向绑定（表单）

**v-text和v-html可以看做是单向绑定，数据影响了视图渲染，但是反过来就不行**

**v-model是双向绑定，视图（View）和模型（Model）之间会互相影响。**

```
语法：写在需要的标签属性位置。
v-model="vue中data的属性"

目前v-model的可使用元素有：
- input
- select
- textarea
- checkbox
- radio
- components（Vue中的自定义组件）
基本上除了最后一项，其它都是表单的输入项。

```

##### v-no事件绑定

v-on指令用于给页面元素绑定事件

```
语法：写在需要的标签属性位置。
格式：v-on:事件名="js片段或函数名"
缩写：@事件名="js片段或函数名”
例如：v-on:click='add'可以简写为@c1ick='add'
```

##### v-for

遍历数据渲染页面是非常常用的需求，Vue中通过v-for指令来实现

```
语法：写在需要的标签属性位置。
v-for="item in items"
v-for="(item,index) in items"
- items：要迭代的数组
- item：迭代得到的数组元素别名
- index：迭代到的当前元素索引，从0开始
```

##### v-if

条件判断。当得到结果为true时，所在的元素才会被渲染

```
语法：写在需要的标签属性位置。
v-if="布尔表达式"
v-else
```

##### Vue生命周期

每个 Vue 实例在被创建时都要经过一系列的初始化过程 ：创建实例，装载模板，渲染模板等等

###### 生命周期的8个监听方法

| 方法名称      | 状态   |
| ------------- | ------ |
| beforeCreate  | 创建前 |
| created       | 创建后 |
| beforeMount   | 挂载前 |
| mounted       | 挂载后 |
| beforeUpdate  | 更新前 |
| updated       | 更新后 |
| beforeDestroy | 销毁前 |
| destroyed     | 销毁后 |

**方法说明**

| 方法名称      | 说明                                                         |
| ------------- | ------------------------------------------------------------ |
| beforeCreate  | 数据还没有监听，没有绑定到vue对象实例，同时也没有挂载对象    |
| created       | 数据已经绑定到了对象实例，但是还没有挂载对象                 |
| beforeMount   | 模板已经编译好了，根据数据和模板已经生成了对应的元素对象，将数据对象关联到了对象的el属性，el属性是一个HTMLElement对象，也就是这个阶段，vue实例通过原生的createElement等方法来创建这个html片段，准备注入到我们vue实例指明的el属性所对应的挂载点 |
| mounted       | 将el的内容挂载到了el，相当于我们在jquery执行了(el).html(el),生成页面上真正的dom，上面我们就会发现dom的元素和我们el的元素是一致的。在此之后，我们能够用方法来获取到el元素下的dom对象，并进行各种操作 |
| beforeUpdate  | 当我们的data发生改变时，会调用beforeUpdate和updated方法：数据更新到dom之前，我们可以看到$el对象已经修改，但是我们页面上dom的数据还没有发生改变 |
| updated       | 当我们的data发生改变时，会调用beforeUpdate和updated方法：dom结构会通过虚拟dom的原则，找到需要更新页面dom结构的最小路径，将改变更新到dom上面，完成更新 |
| beforeDestroy | 实例的销毁，vue实例还是存在的，只是解绑了事件的监听还有watcher对象数据与view的绑定，即数据驱动 |
| destroyed     |                                                              |

 了解生命周期，**掌握常用的 created方法！**  

 可以理解 页面标签数据初始化之前执行的方法！

通常在此方法中，我们发起后台数据请求，在渲染页面标签数据之前，先获取后台数据，进行data 数据的模型赋值！

**mounted方法完成了数据绑定显示。**

#### ElementUI

ElementUI，一套为开发者、设计师和产品经理准备的基于 Vue 2.0的桌面端组件库，提供了配套的设计资源，帮助你快速开发前端页面。

官方网址：https://element.eleme.cn/2.9/#/zh-CN

##### 安装

###### 网络资源

```
<!-- 引入 elementUI 样式 -->
<link rel="stylesheet" href="https://unpkg.com/element-ui/lib/theme-chalk/index.css">
<!-- 引入 vue 的js文件： elementUI基于Vue开发，必须在elementUI的js文件之前引入 -->
<script src="https://cdn.jsdelivr.net/npm/vue"></script>
<!-- 引入elementUI 组件库 -->
<script src="https://unpkg.com/element-ui/lib/index.js"></script>

```

###### 本地资源                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               

```
<!-- 引入 elementUI 样式 -->
<link rel="stylesheet" href="../js/element-ui-2.13.0/lib/theme-chalk/index.css">
<!-- 引入 vue 的js文件： elementUI基于Vue开发，必须在elementUI的js文件之前引入 -->
<script src="../js/vue.js"></script>
<!-- 引入elementUI 组件库 -->
<script src="../js/element-ui-2.13.0/lib/index.js"></script>

```

