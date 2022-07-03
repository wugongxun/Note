# 第一章 HTML和CSS

## 一，HTML

1. 简介

   Hyper Text Markup Language（超文本标记语言）

   HTML通过标签来标记显示的网页中的各个部分，网页文件本身是一种文本文件，通过在文本文件中添加标记符，可以告诉浏览器如何显示其中的内容（如：文字如何处理，画面如何安排，图片如何显示等）

2. 创建HTML文件

   - 创建一个web工程（静态的web工程）
   - 在工程下创建HTML页面

3. HTML文件的书写规范

   ```html
   <!DOCTYPE html><!-- 约束，声明 -->
   <html lang="zh_CN"><!-- html标签表示html的开始 lang="zh_CN"表示中文 html标签中一般分为两部分，分别是head和body -->
   <head><!-- 表示头部信息，一般包含三部分内容title标题，css样式，js代码 -->
       <meta charset="UTF-8"><!-- 表示当前页面使用UTF-8字符集 -->
       <title>标题</title><!-- 表示标题 -->
   </head>
   <body><!-- body标签是整个html页面显示主题的内容 -->
       hello
   </body>
   </html><!-- 表示整个html页面的结束 -->
   ```

4. HTML的标签介绍

   - 标签的格式

     <标签名>封装的数据</标签名>

   - 标签名大小不敏感

   - 标签有自己的属性

     基本属性：bgcolor="red"

     事件属性：onclick="alert('你好！')"

   - 标签又分为单标签和双标签

     单标签格式：<标签名 />		br 换行，hr 水平线

     双标签格式：<标签名> ...封装的数据... </标签名>

   - ```html
     <!DOCTYPE html><!-- 约束，声明 -->
     <html lang="zh_CN"><!-- html标签表示html的开始 lang="zh_CN"表示中文 html标签中一般分为两部分，分别是head和body -->
     <head><!-- 表示头部信息，一般包含三部分内容title标题，css样式，js代码 -->
         <meta charset="UTF-8"><!-- 表示当前页面使用UTF-8字符集 -->
         <title>标题</title><!-- 表示标题 -->
     </head>
     <!-- bgcolor是背景颜色属性
          onclick表示单击事件
          alert()是JavaScript语言提供的一个警告框函数
          他可以接受任意的参数，参数是警告框的函数信息 -->
     <body bgcolor="white"><!-- body标签是整个html页面显示主题的内容 -->
     hello<br/>
     <hr/>
     <button onclick="alert('你好~~')">按钮</button>
     </body>
     </html><!-- 表示整个html页面的结束 -->
     ```

5. HTML标签的语法

   - 标签不能交叉嵌套
   - 标签必须正确关闭
   - 属性必须要有值
   - 注释不能嵌套

6. 常用标签介绍

   - font标签是字体标签，可以修改文本的字体，颜色，大小

     color属性修改颜色

     face属性修改字体

     size属性修改文本大小

7. 特殊字符

   - <：&lt
   - \>：&gt
   - 空格：&nbsp

8. 标题标签

   - h1-h6：从大到小

   - align属性：left左对齐（默认）

     ​					center居中

     ​					right右对齐

9. 超链接

   - \<a>...\<a/>

   - href属性：设置连接的地址

   - target属性：设置那个目标进行跳转

     ​						_self：表示当前页面（默认值）

     ​						_blank：表示打开新的页面进行跳转

10. 列表标签

    - 无序列表

      \<ul type="...">

      ​		\<li>...\</li>

      ​		\<li>...\</li>

      \</ul>

      type可以修改列表项前面的符号

    - 有序列表

      \<ol>\</ol>

11. img标签

    - img标签用来显示图片

    - src属性设置图片路径

    - 在web中

      ​		相对路径：.表示当前文件所在的目录

      ​							..表示当前文件所在的上一级目录

      ​							文件名表示当前文件所在目录的文件，相当于./文件名（./可以省略）

      ​		绝对路径：http://ip:port/工程名/资源路径

    - width属性：设置宽度

    - height属性：设置高度

    - border属性：设置边框

    - alt属性：设置当指定路径找不到图片时，用来代替显示的文本内容

12. table标签

    - table标签用来制作表格

    - \<table border="..." width="..." height="..." align="..." cellspacing="...">

      ​		\<tr>

      ​				\<td align="center" colspan="...">...\</td>

      ​				\<td align="center" rowspan="...">...\</td>

      ​				\<td align="center">...\</td>

      ​				\<th>...\</th>

      ​		\</tr>

      \</table>

    - \<tr>表示行

    - \<td>表示单元格

    - \<th>表示表头

    - \<b>表示加粗

    - 属性border：设置边框

    - 属性width：设置高度

    - 属性height：设置高度

    - 属性align：设置位置

    - 属性cellspacing：设置单元格间距

    - 属性colspan：设置跨行数

    - 属性rowspan：设置跨列数

13. iframe标签

    - iframe标签他可以在一个html页面上打开一个窗口去加载一个单独的页面（内嵌窗口）

    - ```html
      <iframe src="index.html" width="1000" height="1000"></iframe>
      ```

    - iframe标签和a标签组合使用的步骤

      在iframe标签中使用name属性定义一个名称

      在a标签的target属性上设置iframe的name属性值

      ```html
      <iframe src="index.html" width="1000" height="1000" name="我的网站"></iframe>
      <a href="index.html" target="我的网站">我的网站</a>
      ```

14. 表单标签

    - 表单就是html页面中用来收集用户信息的所有元素集合，然后把这些信息发给服务器

    - ```html
      <form action="https://www.baidu.com" method="get"><!--action属性设置提交的服务器地址，method属性设置提交的方式get(默认)或post-->
          用户名称:<input name="user" type="text" value="默认值" /><br/>
          密码:<input name="password" type="password" /><br/>
          性别:<input type="radio" name="sex" value="man"/>男<input type="radio" name="sex" value="woman"/>女<br/>
          兴趣爱好:<input name="hobby" type="checkbox" value="java"/>Java<input name="hobby" type="checkbox" value="C++"/>C++<input name="hobby" type="checkbox" value="JavaScript"/>JavaScript<br/>
          国籍:<select name="nationality">
                  <option value="none">--请选择国籍--</option>
                  <option value="cn" selected="selected">中国</option><!--默认-->
                  <option value="usa">美国</option>
                  <option value="jp">日本</option>
              </select><br/>
          自我评价:<textarea rows="10" cols="20" name="evaluation">默认值</textarea><br/><!--rows属性设置显示几行的高度，cols属性设置每行可以显示几个字符宽度-->
          <input type="reset" value="重置"/><!--重置-->
          <input type="button" value="按钮"/><!--按钮-->
          <input type="submit" value="提交"/><!--提交-->
          <br/>
          <input type="file"/><br/><!--文件上传-->
          <input type="hidden" name="wgx" value="root"/><!--隐藏域，当我们要发送某些信息，而这些信息不需要用户参与，就可以使用隐藏域(提交的时候同时发送给服务器)-->
      </form>
      ```

    - 表单提交时，数据没有发送给服务器的三种情况

      表单项没有name属性值

      单选，复选，下拉列表中的option标签都需要加value属性，以便发送给服务器

      表单项不在提交的form标签中

    - get方法的特点：

      1. 浏览器地址栏中的地址是：action属性+?+请求参数

      2. 请求参数的格式是：name=value&name=value

      3. 不安全
      4. 有数据长度的限制

    - post方法的特点：

      1. 浏览器地址栏中只有action属性值
      2. 相对于get请求安全
      3. 理论上没有数据长度的限制

15. 其他标签div、span、p

    - div标签默认独占一行
    - span标签长度是封装数据的长度
    - p是段落标签默认会在段落的上方或下方各空出一行来（如果已有就不再空）

## 二，CSS

1. 技术介绍

   CSS是层叠样式表单，是用于（增强）控制网页样式并允许样式信息与网页内容分离的一种标记性语言

2. CSS语法规则

   - 选择器：浏览器根据“选择器”决定CSS样式影响的HTML元素（标签）

   - 属性：属性是你要改变的样式名，并且每个属性都有一个值，属性和值被冒号分开，并由花括号包围，这样就组成了一个完整的样式声明，例如：p {color:blue}

   - 多个声明：如果要定义不知一个声明，则需要用分号将每个声明分开，虽然最后一条声明的最后可以不加分号（但尽量在每条声明的末尾都加上分号）例如：

     p {

     ​	color:blue;

     ​	font-size:30px;

     }

   - 注释：/*  */

3. CSS和HTML的结合方式

   - 在标签的style属性上设置“key:value value;”，修改标签样式

     ```html
     <body>
         <div style="border:1px solid red;">标签1</div>
         <div style="border:1px solid red;">标签2</div>
         <span style="border:1px solid red;">标签3</span>
         <!--1.如果标签多，样式多，代码量非常大
             2.可读性非常差
             3.CSS代码没有什么复用性-->
     </body>
     ```

   - 在head标签中，使用style标签来定义自己需要的css样式

     ```html
     <head>
         <meta charset="UTF-8">
         <title>CSS01</title>
         <style>
             /*
             <style>标签专门来定义css样式代码
             只能在同一页面内复用代码，不能在多个页面中复用
             维护起来不方便，实际的项目中会有成千上万个页面，要到每个页面中去修改
             */
             div{
                 border:1px solid red;
             }
             span{
                 border:1px solid red;
             }
         </style>
     </head>
     ```

   - 把css样式写成一个单独的css文件，在通过link标签引入即可复用

     ```html
     <!--link标签专门引入css样式代码-->
     <link rel="stylesheet" type="text/css" href="1.css"/>
     ```

4. CSS选择器

   - 标签名选择器：标签名选择器可以决定哪些标签被动的使用这个样式

     标签名{

     ​		属性: 值;

     }

   - id选择器：id选择器可以让我们通过id属性选择性的去使用这个样式

     #id属性值{

     ​	属性: 值;

     }

   - class选择器：class类型选择器可以通过class属性有效的选择性的去使用这个样式

     .class属性值{

     ​		属性: 值;

     }

   - 组合选择器：组合选择器可以共用同一个css代码

     选择器 1, 选择器 2, 选择器 n{

     ​		属性: 值;

     }

5. 常用样式

   - 字体颜色

     color: red;

   - 高度

     height: 20px;

   - 宽度

     width: 20px;

   - 背景颜色

     background-color: red;

   - 字体样式

     color: red;

     font-size: 20px;

   - 边框

     border: 1px solid red;

   - DIV居中

     margin-left: auto;

     margin-right: auto;

   - 文本居中

     text-align: center;

   - 超链接去下划线

     text-decoration: none;

   - 表格细线

     table{

     ​		border: 1px solid black;/\*设置边框\*/

     ​		border-collapse: collapse;/\*将边框合并\*/

     }

     td, th{

     ​		border: 1px solid black;/\*设置边框\*/

     }

   - 列表去修饰线

     ul{

     ​		list-style: none;

     }

# 第二章 JavaScript语言入门

## 一，基本介绍

1. JavaScript语言诞生主要是完成页面的数据验证，因此他运行在客户端，需要运行浏览器来解析JavaScript代码。js是NetScape网景公司的产品，最早取名为LiveScript，为吸引更多的java程序员，更名为JavaScript
2. JavaScript的特点
   - 交互性：他可以做的就是信息的动态交互
   - 安全性：不允许直接访问本地磁盘
   - 跨平台性：只要是可以解释js的浏览器都可以执行，和平台无关

## 二，JavaScript和HTML代码的结合方式

1. 第一种

   只需要在head标签中或者在body标签中，使用script标签来书写JavaScript代码

2. 第二种

   使用script标签引入单独的JavaScript代码文件

## 三，变量

1. JavaScript的变量类型
   - 数值类型：number
   - 字符串类型：string
   - 对象类型：object
   - 布尔类型：boolean
   - 函数类型：function
2. JavaScript里特殊的值
   - undefined：未定义，所有js变量为赋予初始值的时候，默认值都是undefined
   - null：空值
   - NAN：全称是Not a Number非数，非数值
3. js中定义变量格式
   - var变量名;
   - var变量名 = 值;

## 四，关系运算

1. 等于：== 等于就是简单的数字面值比较
2. 全等于：=== 全等于就是除了字面值的比较之外，还会比较两个变量的数据类型

## 五，逻辑运算

1. 且运算：&&

   当表达式全为真时，返回最后一个表达式的值

   当表达式有一个为假的时候，返回第一个为假的表达式的值

2. 或运算：||

   当表达式全为假时，返回最后一个表达式的值

   只要有一个表达式为真时，返回第一个为真的表达式的值

3. 取反运算：！

4. 在JavaScript语言中，所有的变量都可以作为一个boolean类型的变量去使用

5. 0、null、undefined、""(空串)都认为是false

## 六，数值

1. 数组的定义

   var 数组名 = [];  		//空数组

   var 数组名 = [1, 'abc', true]		//定义数组同时赋值元素

2. JavaScript语言中的数组，只要我们通过下标赋值，那么最大的下标值，就会自动的给数组扩容操作

## 七，函数

1. 函数的定义

   - 可以使用function关键字来定义函数

     使用的格式

     function 函数名(形参列表){

     ​		函数体

     }

     定义带有返回值的函数：直接在函数体中使用return语句

   - var 函数名 = function(形参列表){

     ​		函数体

     }

   - 在JavaScript中重载会直接覆盖掉上一次定义的函数

2. 函数的arguments隐形参数（只在function函数内）

   就是在function函数中不需要定义，但却可以直接用来获取所有参数的变量，叫隐形参数。隐形参数类似于Javase中可变长参数一样，操作类似数组

## 八，JavaScript中自定义对象

1. 对象的定义

   - var 变量名 = new Object();		//对象实例

   - 变量名.属性名 = 值;		//定义一个属性

   - 变量名.函数名 = function(){}		//定义一个函数

2. 对象的访问

   - 变量名.属性/函数名();

3. {}花括号形式的自定义对象

   - var 变量 = {};		//空对象

   - var 变量 = {

     ​		属性名：值，		//定义一个属性

     ​		属性名：值，		//定义一个属性

     ​		函数名：function(){}	//定义一个函数

     };

## 九，JavaScript中的事件

1. 什么是事件

   事件是电脑输入设备与页面进行页面进行交互的响应，我们称之为事件

2. 常用事件

   - onload：加载完成事件，页面加载完成之后，常用于做页面js代码初始化操作
   - onclick：单击事件，常用于按钮的点击响应操作
   - onblur：失去焦点事件，常用于输入框失去焦点后验证其输入内容是否合法
   - onchange：内容发生改变事件，常用于下拉列表和输入框内容发生改变后操作
   - onsubmit：表单提交事件常用于表单提交前，验证所有表单项是否合法

3. 事件的注册

   - 事件注册就是告诉浏览器，当事件响应后要执行那些操作代码，叫事件注册或者事件绑定

   - 静态事件注册：通过HTML标签的时间属性直接赋予事件响应后的代码，这种方式我们叫静态注册

   - 动态注册事件：是指先通过js代码得到标签的dom对象，然后通过dom对象.事件名 = function(){} 这种形式赋予事件响应后的代码，叫动态注册

   - onclick事件:

     ```html
     <!DOCTYPE html>
     <html lang="en">
     <head>
         <meta charset="UTF-8">
         <title>Title</title>
         <script type="text/javascript">
             function fun1() {
                 alert("静态注册onclick事件");
             }
             window.onload = function () {
                 var buttonObj = document.getElementById("button001");
                 buttonObj.onclick = function () {
                     alert("动态注册onclick事件");
                 }
             }
         </script>
     </head>
     <body>
     <button onclick="fun1()">按钮1</button>
     <button id="button001">按钮2</button>
     </body>
     </html>
     ```

   - onsubmit事件:

     ```html
     <!DOCTYPE html>
     <html lang="en">
     <head>
         <meta charset="UTF-8">
         <title>Title</title>
         <script type="text/javascript">
             function onsubmitFun() {
                 alert("静态注册表单提交事件————提交成功");
                 return true;
             }
             window.onload = function () {
                 var submitObj = document.getElementById("submit001");
                 submitObj.onsubmit = function () {
                     alert("动态注册表单提交事件————提交失败");
                     return false;
                 }
             }
         </script>
     </head>
     <body>
         <form action="https://www.baidu.com" method="get" onsubmit="return onsubmitFun()">
             <input type="submit" value="静态注册"/>
         </form>
         <form action="https://www.baidu.com" method="get" id="submit001">
             <input type="submit" value="动态注册"/>
         </form>
     </body>
     </html>
     ```

## 十，document对象

1. DOM模型

   - DOM全称是Document Object Model 文档对象模型

   - 就是把文档中的标签，属性，文本转换成为对象来管理

2. document对象

   - Document管理了所有的HTML文档内容
   - document是一种树结构的文档，有层级关系
   - document让我们把所有标签都对象化
   - 我们可以通过document对象访问所有的标签对象

3. Document对象中的方法介绍

   - document.getElementById(elementId)

     通过标签的id属性查找标签的dom对象，elementId是标签的id属性值

   - document.getElementsByName(elementName)

     通过标签的name属性查找标签dom对象，elementName标签的name属性值

   - document.getElementsByTagName(tagName)

     通过标签名查找标签dom对象，tagName是标签名

   - document.createElement(tagName)

     通过给定的标签名，创建一个标签对象，tagName是要创建的标签名

4. 节点的常用属性和方法

   - 节点：节点就是标签对象

   - 方法：通过具体的元素节点调用

   - getElementsByTagName()

     获取当前节点的指定标签名孩子节点

   - appendChild(oChildNode)

     可以添加一个子节点，oChildNode是要添加的孩子节点

   - 属性：

     - childNodes：获取当前节点的所有子节点
     - firstChild：获取当前节点的第一个子节点
     - lastChild：获取当前节点的最后一个子节点
     - parentNode：获取当前节点的父节点
     - nextSibling：获取当前节点的下一个节点
     - previousSibling：获取当前节点的上一个节点
     - className：用于获取或设置标签的class属性值
     - innerHTML：表示获取/设置起始标签和结束标签的内容
     - innerText：表示获取/设置起始标签和结束标签中的文本

# 第三章 jQuery

## 一，基本介绍

1. jQuery就是JavaScript和查询（Query），他就是JavaScript开发的js类库
2. jQuery的核心思想是write less，do more（写的少，做的更多），所有实现了很多浏览器的兼容问题
3. JQuery是免费、开源的，jQuery的语法设计可以使开发桁架便捷，例如操作文档对象、选择DOM元素、制作动画效果、事件处理、使用Ajax以及其他功能

## 二，快速入门

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="../jquery-3.6.0.min.js"></script>
    <script>
        $(function () {//表示页面加载完成之后，相当于window.onload = function() {}
            var $buttonObj = $("#buttonId");//表示按Id查询标签对象
            $buttonObj.click(function () {//绑定单击事件
                alert("Hello,World");
            });
        });
    </script>
</head>
<body>
    <button id="buttonId">SayHello</button>
</body>
</html>
```

## 三，jQuery核心函数

1. $是jQuery的核心函数，能完成jQuery的很多功能，$()就是调用$这个函数

2. 传入参数为函数时，表示页面加载完成之后

   相当于window.onload = function() {}

3. 传入参数为HTML字符串时，会帮我们创建这个HTML标签对象

4. 传入参数为选择器字符串时

   $("#id属性值");  id选择器，根据id查询标签对象

   $("标签名");  标签名选择器，根据指定的标签名查询标签对象

   $(".class属性值");  类型选择器，可以根据class属性查询标签对象

5. 传入参数为DOM对象时，会把这个DOM对象转换为jQuery对象

## 四，jQuery对象和DOM对象区分

1. 什么是jQuery对象，什么是DOM对象

   - DOM对象
     - 通过getElementById()查询出来的标签对象是DOM对象
     - 通过getElementsByName()查询出来的标签对象是DOM对象
     - 通过getElementsByTagName()查询出来的标签对象是DOM对象
     - 通过createElement()创建出来的对象是DOM对象
   - jQuery对象
     - 通过jQuery提供的API创建的对象是jQuery对象
     - 通过jQuery包装的DOM对象也是jQuery对象
     - 通过jQuery提供的API查询到的对象是jQuery对象

2. jQuery对象的本质是什么

   - jQuery对象是DOM对象数组+jQuery提供的一系列功能函数

3. jQuery对象和DOM对象的使用区别

   - jQuery对象不能使用DOM对象的属性和方法
   - DOM对象不能使用jQuery对象的属性和方法

4. jQuery对象和DOM对象的相互转换

   - DOM对象转化为jQuery对象

     $(DOM对象) 就可以转换成为jQuery对象

   - jQuery对象转化为DOM对象

     jQuery对象[下标] 取出相应的DOM对象

## 五，jQuery选择器

1. 基本选择器
   - #id	选择器：根据id查找标签对象
   - .class  选择器：根据class查找标签对象
   - element 选择器：根据标签名查找对象
   - \*              选择器：表示任意的，所有元素
   - select1，select2组合选择器：合并选择器1，选择器2的结果返回
2. 层级选择器
   - ancestor descendant	后代选择器：在给定的祖先元素下匹配所有的后代元素
   - parent>child                   子元素选择器：在给定的父元素下匹配所有的子元素
   - prev+next                        相邻元素选择器：匹配所有紧接在prev元素后的next元素
   - prev~siblings                   之后的兄弟元素：匹配prev之后的所有siblings元素
3. 基本过滤选择器
   - :first		获取第一个元素
   - :last        获取最后一个元素
   - :not(selector)         取出所有与给定选择器匹配的元素
   - :even      匹配所有索引值为偶数的元素，从0开始计数
   - :odd        匹配所有索引值为计数的元素，从0开始计数
   - :eq(index)            匹配一个给定索引值的元素
   - :gt(index)             匹配一个所有大于给定索引值的元素
   - :lt(index)              匹配一个所有小于给定索引值的元素
   - :header                匹配如h1, h2, h3之类的标题元素
   - :animated            匹配所有正在执行动画的元素
4. 内容过滤器
   - :contains(text)    匹配包含给定文本的元素
   - :empty                 匹配所有不包含子元素或者文本空元素
   - :parent                 匹配包含子元素或者文本的元素
   - :has(selector)      匹配含有选择器所匹配的元素的元素
5. 属性过滤器
   - [attribute]            匹配包含给定属性的元素
   - [attribute=value]              匹配给定的属性的某个特定值的元素
   - [attribute!=value]             匹配所有不含有指定的属性，或者属性不等于特定值的元素
   - [attribute^=value]            匹配给定的属性是以某些值开始的元素
   - [attribute$=value]            匹配给定的属性是以某些值结尾的元素
   - [attribute*=value]            匹配给定的属性包含某些值的元素
   - \[attrSel1][attrSel2]\[attrSelN]          复合属性选择器，需要同时满足多个条件时使用
6. 表单过滤器
   - :input           匹配所有input，textarea，select和button元素
   - :text              匹配所有文本输入框
   - :password    匹配所有密码输入框
   - :radio            匹配所有单选框
   - :checkbox     匹配所有的复选框
   - :submit         匹配所有的提交按钮
   - :reset             匹配所有重置按钮
   - :button          匹配所有按钮
   - :file                 匹配所有文件域
   - :hidden          匹配所有不可见的元素，或者type为hidden的元素
   - :enabled        匹配所有可用的元素
   - :disabled        匹配所有不可用元素
   - :checked        匹配所有选中的被选中的元素（复选框、单选框等，不包含select中的option）
7. 元素的筛选
   - ![](F:\截图\捕获123.PNG)
   - ![](F:\截图\捕获124.PNG)

## 六，jQuery的属性操作

1. html()，text()，val()
   - html()：他可以设置和获取起始标签和结束标签中的内容，和DOM属性innerHTML一样
   - text()：他可以设置和获取起始标签和结束标签中的文本，和DOM属性innerText一样
   - val()：他可以设置和获取表单项的value属性值，和DOM属性value一样
2. attr()，prop()
   - attr()：可以设置和获取属性值，不推荐操作checked，readOnly，selected，disable等
   - prop()：可以设置和获取属性值，只推荐操作checked，readOnly，selected，disable等

## 七，DOM的增，删，改

1. 内部插入
   - appendTo()		a.appendTo(b)		把a插入到b子元素的末尾，成为最后一个子元素
   - prependTo()      a.prependTo(b)       把a插入到b所有子元素前面，成为第一个子元素
2. 外部插入
   - insertAfter()             a.insertAfter(b)            得到ba
   - insertBefore()          a.insertBefore(b)         得到ab
3. 替换
   - replaceWith()           a.replaceWith(b)          用b替换掉a
   - replaceAll()               a.replaceAll(b)             用b替换掉所有b
4. 删除
   - remove()                   a.remove()                   删除a标签
   - empty()                     a.empty()                      清空a标签里的内容

## 八，CSS样式操作

1. addClass()                添加样式
2. removeClass()         删除样式
3. toggleClass()            有就删除，没有就添加样式
4. offset()                      获取和设置元素的坐标

## 九，jQuery动画

1. 基本动画
   - show()               将隐藏的元素显示
   - hide()                 将可见的元素隐藏
   - toggle()              可见就隐藏，不可见就显示
   - 以上的动画方法都可以添加参数
     - 第一个参数是动画执行的时长，以毫秒为单位
     - 第二个参数是动画的回调函数（动画完成后自动调用的函数）
2. 淡入淡出的动画
   - fadeIn()                淡入
   - fadeOut()             淡出
   - fadeToggle()        淡入/淡出切换
   - fadeTo()               在指定时长内慢慢的将透明度修改到指定值，0透明，1完成，0.5半透明

## 十，jQuery事件操作

1. $(function(){});和window.onload=function(){}的区别

   - 什么时候触发？
     - jQuery的页面加载完成之后是浏览器的内核解析完页面的标签创建好DOM对线时候就会马上执行
     - 原生js的页面加载完成之后，除了要等浏览器内核解析完标签创建好的DOM对象，还要等标签显示时需要的内容加载完成
   - 触发顺序？
     - jQuery页面加载完成之后先执行
     - 原生js的页面加载完成之后
   - 执行的次数？
     - 原生js的页面加载完成之后，只会执行最后一次赋值函数
     - jQuery的页面加载完成之后，全部把注册的function函数，依次顺序全部执行

2. jQuery中其他的事件处理方法

   - click()                他可以绑定单击事件，以及触发单击事件
   - mouseover()    鼠标移入事件
   - mouseout()      鼠标移出事件
   - bind()                 可以给元素一次性绑定一个或多个事件
   - one()                  使用上和bind一样，但是one方法绑定的事件只会响应一次
   - unbind()             和bind方法相反的操作，解除事件的绑定
   - live()                    也是用来绑定事件，他可以用来绑定选择器匹配的所有元素的事件，哪怕这个元素是后面动态创建出来的也有效

3. 事件的冒泡

   - 事件的冒泡是指，父子元素同时监听同一个事件。当触发子元素的事件的时候，同一个事件也被传递到了父元素事件里去响应

   - 如何阻止事件冒泡

     在子元素事件函数内，return false；可以阻止事件冒泡的传递

4. JavaScript事件对象

   - 事件对象，是封装有触发的事件信息的一个JavaScript对象

   - 如何获取JavaScript事件对象？

     再给元素绑定事件的时候，在事件的function(event)参数列表中添加一个参数，这个参数名我们习惯取名为event，这个event就是JavaScript传递事件处理函数的事件对象

# 第四章 XML

## 一，基本介绍

1. 什么是XML

   XML是可扩展标记性语言

2. XML的作用是什么

   - 用来保存数据，而且这些数据具有自我描述性
   - 他还可以作为项目或者模块的配置文件
   - 还可以作为网络传输数据的格式（现在是以JSON为主）

## 二，XML语法

1. 文档声明

   元素（标签）

   XML属性

   XML注释

   文本区域（CDATA区）

2. ```xml
   <?xml version="1.0" encoding="utf-8" ?>
   <!--
       <?xml version="1.0" encoding="utf-8" ?>就是XML文件的声明
       version="1.0"       表示XML的版本
       encoding="utf-8"    表示XML文件本身的编码
   -->
   <books>
       <book sn="123456789"><!--book表示一个图书信息，sn属性表示图书序列号-->
           <name>java核心技术卷1</name><!--书名-->
           <author>Cay S. Horstmann</author><!--作者-->
           <price>75</price><!--图书价格-->
       </book>
       <!--...多个图书-->
   </books>
   ```

3. 元素（标签）

   - XML元素指的是从（且包括）开始标签到结束标签的部分，元素可以包含其他元素、文本或者两者的混合物。元素也可以拥有属性

4. XML元素必须遵守以下命名规则

   - 名称可以含字母、数字以及其他字符
   - 名称不能以数字或者标点符号开始
   - 名称不能包含空格

5. XML汇总的元素（标签）也分成单标签和双标签

   - 单标签：<标签名 属性="值" 属性="值" ... />
   - 双标签：<标签名 属性="值" 属性="值" ...>文本数据或者子标签</标签名>

6. XML属性

   - XML的标签属性和HTML的标签属性是非常类似的，属性可以提供元素的额外信息
   - 一个标签上可以书写多个属性，每个属性的值必须使用引号引起来。规则和标签的书写规则一致

7. XML语法规则

   - 所有XML元素都必须关闭标签（也就是闭合）

   - XML标签对大小写敏感

   - XML标签必须正确的嵌套

   - XML文档必须有根元素（根元素就是顶级元素，没有父标签的元素叫顶级元素，根元素是没有父标签的顶级元素，而且是唯一一个才行）

   - XML中的属性值必须加引号

   - XML中的特殊字符：>特殊字符，\&gt;<特殊字符，\&lt;

   - 文本区域（CDATA区）

     CDATA语法可以告诉XML解析器，CDATA力里的文本内容，只是纯文本不需要XML语法解析

     CDATA格式：\<![CDATA[这里可以把你输入的字符原样显示，不会解析XML]]>

## 三，XML解析技术介绍

1. 不管是HTML文件还是XML文件他们都是标记型文档，都可以使用w3c组织制定的dom技术来解析

2. 由于dom4j不是sun公司的技术，而属于第三方公司技术，我们需要使用dom4j就需要到dom4j官网下载dom4j的jar包

3. dom4j编程步骤

   - 先加载XML文件创建Document对象
   - 通过Document对象拿到根元素对象
   - 通过根元素.element(标签名)；可以返回一个集合，这个集合里放着所有指定的标签名的元素对象
   - 找到你先要修改、删除的子元素，进行相应在的操作
   - 保存到磁盘上

4. 获取document对象

   ```java
   //1. 读取books.xml文件
   SAXReader saxReader = new SAXReader();
   Document document = saxReader.read("src/books.xml");
   //2. 通过Document对象获取根元素
   Element rootElement = document.getRootElement();
   //3. 通过根元素获取book标签对象
   //   element()和elements()都是通过标签名查找子元素
   List<Element> books = rootElement.elements("book");
   //4. 遍历，处理每个book标签转换为标签字符串
   for (Element book : books) {
       //asXML()把标签对象转换成标签字符串
       Element name = book.element("name");
       System.out.println(name.asXML());
       //getText()可以获取标签中的文本内容
       String nameText = name.getText();
       System.out.println(nameText);
       //直接获取指定标签名的文本内容
       String priceText = book.elementText("price");
       System.out.println(priceText);
       String authorText = book.elementText("author");
       //attributeValue()获取属性值
       String snValue = book.attributeValue("sn");
       System.out.println(snValue);
       System.out.println(new Book(snValue, nameText, authorText, Double.parseDouble(priceText)));
   }
   ```

# 第五章 Tomcat

## 一， JavaWeb的概念

1. 什么是JavaWeb

   javaweb是指所有通过java语言编写可以通过浏览器访问的程序的总称，叫javaweb

   javaweb是基于请求和响应来开发的

2. 什么是请求

   请求是指客户端给服务器发送数据，叫请求Request

3. 什么是响应

   响应是指服务器给客户端回传数据，叫响应Response

4. 请求和响应的关系

   请求和响应是成对出现的，有请求就有响应

5. Web资源的分类

   web资源按实现的技术和呈现的效果不同，有分为静态资源和动态资源两种

   静态资源：HTML、CSS、JS、TXT、MP4视频、jpg图片

   动态资源：JSP页面、servlet程序

6. 常用的Web服务器

   ![](F:\截图\捕获125.PNG)

## 二，Tomcat

1. Tomcat安装与启动停止

   - 安装，直接解压到需要安装的文件夹

   - 启动，双击Tomcat目录下的bin目录下的startup.bat文件

   - 测试Tomcat服务器启动成功

     在浏览器地址栏中输入http://localhost:8080地址测试

   - 另一种启动Tomcat服务器的方式

     在Tomcat的bin目录下输入启动命令catalina run

   - 停止Tomcat

     - 直接关闭Tomcat服务器窗口
     - 把Tomcat服务器窗口置为当前窗口，然后Ctrl+C
     - 找到Tomcat的bin目录下的shutdown.bat

2. 修改Tomcat的端口号

   MySQL默认端口号是3306

   Tomcat默认端口号是8080

   找到Tomcat目录下的conf目录，找到server.xml配置文件，修改Connector port="8080"，重启生效

3. 部署web工程的方法

   - 只需要在浏览器中输入访问地址格式如下：http://ip:prot/工程名/目录/文件名

   - 找到Tomcat下的conf\Catalina\localhost\下创建如下配置文件

     ```xml
     <Context path="/Library" docBase="E\Library"/>
     <!-- Context表示一个工程上下文
     	path表示工程访问路径
     	docBase表示你的工程目录在哪
     -->
     ```

4. ROOT工程的访问，以及index.html页面的访问

   - http://ip:prot/		没有工程名的时候，默认访问的是ROOT工程
   - http://ip:prot/工程名/            没有资源名的时候，默认访问index.html页面

# 第六章 Servlet

## 一，Servlet技术

1. 什么是Servlet

   - Servlet是JavaEE规范之一，规范就是接口
   - Servlet就是javaweb三大组件之一，三大组件分别是Servlet程序，Filter过滤器，Listener监听器
   - Servlet是运行在服务器上的一个java小程序，他可以接受客户端发送过来的请求，并响应数据给客户端

2.  手动实现Servlet程序

   - 编写一个类去实现Servlet接口

   - 实现service方法处理请求，并响应数据

   - 到web.xml中去配置servlet程序的访问地址

   - ```xml
     <?xml version="1.0" encoding="UTF-8"?>
     <web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
              xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
              xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
              version="4.0">
         <!--servlet标签给Tomcat配置Servlet程序-->
         <servlet>
             <!--servlet-name标签表示给Servlet程序起一个别名(一般是类名)-->
             <servlet-name>HelloServlet</servlet-name>
             <!--servlet-class是Servlet程序的全类名-->
             <servlet-class>com.wgx.servlet.HelloServlet</servlet-class>
             <!--init-param表示初始化参数-->
             <init-param>
                 <param-name>username</param-name>
                 <param-value>root</param-value>
             </init-param>
             <init-param>
                 <param-name>url</param-name>
                 <param-value>jdbc:mysql://localhost:3306</param-value>
             </init-param>
         </servlet>
         <!--servlet-mapping标签给Servlet程序配置访问地址-->
         <servlet-mapping>
             <!--servlet-name告诉服务器，我当前配置的地址给那个Servlet程序使用-->
             <servlet-name>HelloServlet</servlet-name>
             <!--url-pattern标签配置访问地址
                 / 表示在服务器解析的时候，表示地址为http://ip:port/工程路径
                 /hello 表示地址为http://ip:port/工程路径/hello
             -->
             <url-pattern>/hello</url-pattern>
         </servlet-mapping>
     </web-app>
     ```

3. Servlet的生命周期

   - 执行Servlet构造器方法

   - 执行init初始化方法

   - 前两步是在第一次访问的时候创建Servlet程序调用

     ---

   - 执行service方法

   - 第三步每次访问都会调用

   - ---

     执行destroy销毁方法

   - 第四步在web工程停止的时候调用

4. 通过继承HttpServlet实现Servlet程序

   - 编写一个类去继承HttpServlet类
   - 根据业务需要重写doGet或者doPost方法
   - 到web.xml中的配置Servlet程序的访问地址

## 二，ServletConfig类

1. ServletConfig类的三大作用

   - 可以获取Servlet程序的别名servlet-name的值

   - 获取初始化参数init-param

   - 获取ServletContext对象

   - ```java
     public void init(ServletConfig servletConfig) throws ServletException {
             //获取Servlet程序的别名servlet-name的值
             System.out.println("初始化的HelloServlet程序的别名:" + servletConfig.getServletName());
             //获取初始化参数context-value
             System.out.println("初始化参数username的值:" + servletConfig.getInitParameter("username"));
             System.out.println("初始化参数url的值:" + servletConfig.getInitParameter("url"));
             //获取ServletContext对象
             System.out.println(servletConfig.getServletContext());
         }
     ```

## 三，ServletContext类

1. 什么是ServletContext

   - ServletContext是一个接口，他表示Servlet上下文对象

   - 一个web工程只有一个ServletContext对象实例

   - ServletContext对象是一个域对象

   - ServletContext是在web工程部署启动的时候创建，在web工程停止的时候销毁

   - 域对象是可以向Map一样存取数据的对象，叫域对象，这里的域指的是存取数据的操作范围，整个web工程

   - |        | 存数据         | 取数据         | 删除数据          |
     | ------ | -------------- | -------------- | ----------------- |
     | Map    | put()          | get()          | remove()          |
     | 域对象 | setAttribute() | getAttribute() | removeAttribute() |

2. ServletContext类的四个作用

   - 获取web.xml中配置的上下文参数context-param
   - 获取当前的工程路径，格式：/工程路径
   - 获取工程部署后在服务器硬盘上的绝对路径
   - 想Map一样存取数据

## 四，HTTP协议

1. 什么是协议

   协议是双方或者多方互相约定好，大家都要遵守规则，叫协议

   所谓HTTP协议就是指，客户端和服务器之间通信时，发送的数据，需要遵守的规则叫HTTP协议

   HTTP协议中的数据又叫报文

2. GET请求

   - 请求行
     - 请求的方式		GET
     - 请求的资源路径[+?+请求参数]
     - 请求的协议的版本号          HTTP/1.1
   - 请求头
     - key: value		不同的键值对，表示不同的含义

3. POST请求

   - 请求行

     - 请求的方式		GET
     - 请求的资源路径[+?+请求参数]
     - 请求的协议的版本号          HTTP/1.1

   - 请求头

     - key: value		不同的键值对，表示不同的含义

     ---

     ==空行==

   - 请求体               发送给服务器的数据

4. 常见的请求头说明

   - Accept：表示客户端可以接受的数据类型
   - Accept-Language：表示客户端可以接受的语言类型
   - User-Agent：表示客户端浏览器的信息
   - Host：表示请求时的服务器ip和端口号

5. 那些是GET请求，那些是POST请求

   - GET请求
     - form标签method=get
     - a标签
     - link标签引入CSS
     - Script标签引入js文件
     - img标签引入图片
     - iframe引入HTML页面
     - 在浏览器地址栏中输入地址后敲回车
   - POST请求
     - form标签method=post

6. 响应HTTP协议格式

   - 响应行

     - 响应的协议和版本号
     - 响应状态码
     - 响应状态描述符

   - 响应头

     - key: value              不同的响应头，有其不同的含义

     ---

     ==空行==

   - 响应体                            就是回传给客户端的数据

7. 常见的响应码说明

   - 200  表示请求成功
   - 302  表示请求重定向
   - 404  表示请求服务器已经收到了，但是你要的数据不存在（请求地址错误）
   - 500  表示服务器已经收到请求，但是服务器内部错误（代码错误）

8. MIME类型说明

   MIME是HTTP协议中数据类型

   MIME的英文全称是“Multipurpose Internet Mail Extensions”多功能Internet邮件扩充服务。MIME类型格式是“大类型/小类型”，并与某一种文件的扩展名相对应

   ![](F:\截图\捕获126.PNG)

## 五，HttpServletRequest类

1. HttpServletRequest类有什么用

   每次只要有请求进入到Tomcat服务器，Tomcat服务器就会把请求过来的HTTP协议信息解析好封装到Request对象中，然后传递到service方法（doGet和doPost）中给我们使用，我们就可以通过HttpServletRequest对象，获取所有请求的信息

2. HttpServletRequest类的常用方法

   ![](F:\截图\捕获127.PNG)

   ```java
   protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
       //1. 获取请求的资源路径
       System.out.println("URI:" + req.getRequestURI());
       //2. 获取请求的同一资源定位符(绝对路径)
       System.out.println("URL:" + req.getRequestURL());
       //3. 获取客户端ip地址
       System.out.println("客户端ip地址:" + req.getRemoteHost());
       //4. 获取请求头
       System.out.println("请求头User-Agent:" + req.getHeader("User-Agent"));
       //5. 获取请求方式
       System.out.println("请求方式:" + req.getMethod());
   }
   ```

3. 获取请求的参数值

   ```java
   protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
       System.out.println("用户名:" + req.getParameter("username"));
       System.out.println("密码:" + req.getParameter("password"));
       System.out.println("兴趣爱好:" + Arrays.asList(req.getParameterValues("hobby")));
   }
   ```

4. 请求的转发

   - 请求转发是指，服务器收到请求后从一次资源跳转到另一个资源的操作叫请求转发

   - ```java
     public class Servlet1 extends HttpServlet {
         @Override
         protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
             //获取请求的参数(办事的材料)查看
             String username = req.getParameter("username");
             //给材料盖一个章，并传递给Servlet2(柜台2)去查看
             req.setAttribute("key", "柜台1的章");
             //问路:柜台2怎么走
             RequestDispatcher requestDispatcher = req.getRequestDispatcher("/servlet2");
             //走到Servlet2(柜台2)
             requestDispatcher.forward(req, resp);
         }
     }
     ```

   - ```java
     public class Servlet2 extends HttpServlet {
         @Override
         protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
             //获取请求的参数(办事的材料)查看
             String username = req.getParameter("username");
             //查看柜台1是否有盖章
             Object key = req.getAttribute("key");
             System.out.println("柜台1的章:" + key);
             //处理自己的业务
             System.out.println("Servlet2处理自己的业务");
         }
     }
     ```

   - 请求转发的特点

     - 浏览器地址栏没有变化
     - 他们是一次请求
     - 他们共享Request域中的数据
     - 可以转发到WEB-INF目录下
     - 不可以访问工程以外的资源

5. base标签

   - base标签设置页面相对路径工作时参照的地址，href属性就是参照的地址值

## 六，HttpServletResponse类

1. HttpServletResponse类的作用

   HttpServletResponse类和HttpServletRequest类一样，每次请求进来，Tomcat服务器都会创建一个Response对象传递给Servlet程序去使用，HttpServletRequest表示请求过来的信息，HttpServletResponse表示所有响应信息，我们如果需要设置返回给客户端的信息，都可以通过HttpServletResponse对象来进行设置

2. Hello，World

   - ```java
     public class ResponseServlet extends HttpServlet {
         @Override
         protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
             //1. 设置服务器字符集为UTF-8
             resp.setCharacterEncoding("UTF-8");
             //2. 设置响应头，设置浏览器也是用UTF-8字符集
             resp.setHeader("Content-Type", "text/html;charset=UTF-8");
             //3. 获得字符流
             PrintWriter writer = resp.getWriter();
             //4. 输出
             writer.write("Hello,World 你好");
         }
     }
     ```

   - ```java
     public class ResponseServlet extends HttpServlet {
         @Override
         protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
             //1. 使用setContentType()设置客户端和服务器都使用UTF-8字符集，还设置了响应头
             resp.setContentType("text/html;charset=UTF-8");
             //2. 获得字符流
             PrintWriter writer = resp.getWriter();
             //3. 输出
             writer.write("Hello,World 你好");
         }
     }
     ```

3. 请求重定向（第一种）

   - 请求重定向是指客户端给服务器发请求，然后服务器告诉客户端说，我给你一些地址，你去新的地址访问。叫请求重定向（因为之前的地址可能已经被废弃）

   - Response1

     ```java
     public class Response1 extends HttpServlet {
         @Override
         protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
             //1. 设置状态码为302，表示请求重定向
             resp.setStatus(302);
             //2. 设置请求头
             resp.setHeader("Location", "http://localhost:8080/Response_war_exploded/response2");
         }
     }
     ```

   - Response2

     ```java
     public class Response2 extends HttpServlet {
         @Override
         protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
             resp.setContentType("text/html;charset=UTF-8");
             resp.getWriter().write("response2的响应");
         }
     }
     ```

   - 请求重定向的特点

     - 浏览器地址栏会发生变化
     - 两次请求
     - 不共享Response域中的数据
     - 不能访问WEB-INF下的资源
     - 可以访问工程以外的资源

4. 请求重定向（第二种）

   - Response1

     ```java
     public class Response1 extends HttpServlet {
         @Override
         protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
             resp.sendRedirect("http://localhost:8080/Response_war_exploded/response2");
         }
     }
     ```


## 七，JavaEE三层架构

1. ![](F:\截图\捕获128.PNG)
2. ![](F:\截图\捕获129.PNG)

# 第七章 JSP

## 一，JSP本质

1. jsp的全称是java server pages。java的服务器页面

   jsp的主要作用是代替Servlet程序回传HTML页面的数据

   因为Servlet程序回传HTML页面是一个非常繁琐的事情，开发成本和维护成本都极高

2. jsp本质上是一个Servlet程序

   当我们第一次访问jsp页面的时候，Tomcat服务器会把jsp页面翻译成为一个java源文件，并对他编译成为.class字节码程序。这个java源代码里是一个类继承了HttpJspBase类，HttpJspBase继承了HttpServlet类，也就是说它间接继承了HttpServlet类，是一个Servlet程序

## 二，jsp头部的page指令

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
```

1. language属性：表示jsp翻译后是什么语言文件，暂时只支持java
2. contentType属性：表示jsp返回的数据类型是什么，也是源码中response.setContentType()参数值
3. pageEncoding属性：表示当前jsp页面文件本身的字符集
4. import属性：和java源代码中一样，用于导包，导类
5. autoFlush属性：设置当前out输出流缓冲区满了之后是否自动刷新缓冲区，默认值是true
6. buffer属性：设置out缓冲区的大小，默认值是8kb
7. errorPage属性：设置当jsp页面运行时出错，自动跳转去错误页面路径
8. isErrorPage属性：设置当前jsp页面是否是错误信息页面，默认是false，如果设置为true可以获取异常信息
9. session属性：设置访问当前jsp页面是否会创建HttpSession对象，默认是true
10. extends属性：设置jsp翻译出来的java类默认继承谁

## 三，jsp中的常用脚本

1. 声明脚本的格式：<%! 声明java代码 %>

   可以给jsp翻译出来的java类定义属性和方法升值是静态代码块，内部类等

2. 表达式脚本格式：<%= 表达式脚本 %>

   表达式脚本作用：在jsp页面上输出数据

   表达式脚本的特点：

   - 所有的表达式脚本都会被翻译到_jspService方法中
   - 表达式脚本都会被翻译成为out.print()输出到页面上
   - 由于表达式脚本翻译的内容都在_jspService()方法中，所有-jspService()方法中的对象都可以直接使用
   - 表达式脚本中的表达式不能以分号结束

3. 代码脚本格式：<% java语句 %>

   代码脚本的作用：可以在jsp页面中，编写我们需要的功能

   代码脚本的特点：

   - 代码脚本翻译之后能都在_jspService()方法中
   - 代码脚本由于翻译到_jspService()方法中，所有在\_jspService()方法中的现有对象都可以直接使用
   - 还可以由多个代码脚本块组合完成一个完整的java语句
   - 代码脚本还可以和表达式脚本一起组合使用，在jsp页面上输出数据

## 四，jsp中的三种注释

1. HTML注释

   <!-- 这里是HTML注释 -->

   HTML注释会被翻译到java源代码中，在_jspService()方法里，以out.writer输出到客户端

2. java注释

   //单行java注释

   /* 多行java注释 */

   java注释会被翻译到java源代码汇总

3. jsp注释

   <% 这里是jsp注释 %>

   jsp注释可以注释掉jsp页面中所有的代码

## 五，jsp中的九大内置对象

1. jsp中的内置对象是指Tomcat在翻译jsp页面称为Servlet源代码后，内部提供的九大对象，叫内置对象
2. ![](F:\截图\捕获130.PNG)
3. 四大域对象
   - pageContext             （PageContextImpl类）				当前jsp页面范围有效
   - request                      （HttpServiceRequest类）           一次请求内有效
   - session                       （HttpSession类）                         一次会话范围内有效（打开浏览器访问服务器，直到关闭浏览器）
   - application                 （ServletContext类）                   整个web工程范围内有效（只要web工程不停止，数据就在）
   - 使用顺序：pageContext  ->  request  ->  session  ->  application
4. jsp中out和response.getWriter输出的区别
   - 当页面中所有代码执行完成后会做一下两个操作
     - 执行out.flush()操作，会把out缓冲区中的数据追加到response缓冲区末尾
     - 执行response的刷新操作，把全部数据写给客户端
   - 由于jsp翻译之后底层源码都是使用out输出，所有一般情况下在jsp页面中统一使用out来进行输出，避免打乱页面输出内容顺序
   - out.writer()只适用输出字符串
   - out.print()输出任意数据没有问题（都转换成为字符串调用writer输出）

## 六，jsp常用标签

1. 静态包含

   - <%@ include file="" %>

     file属性指定你要包含的jsp页面的路径

     地址中第一个/表示http://ip:port/工程路径/		映射到代码的web路径

   - 静态包含的特点
     1. 静态包含不会翻译被包含的jsp页面
     2. 静态包含其实就是把被包含的jsp页面的代码拷贝到包含的位置执行输出
   
2. 动态包含

   - <jsp:include page="">\<jsp:include>

     page属性是指定你要包含的jsp页面的路径

     动态包含也可以像静态包含一样，把被包含的内容执行输出到包含位置

   - 动态包含的特点：

     - 动态包含会把被包含的jsp页面也翻译成为java代码
     - 动态包含，还可以传递参数

3. 请求转发标签

   - <jsp:forward page="">\<jsp:forward>

4. Listener监听器

   - Listener监听器是javaweb三大组件之一，javaweb的三大组件分别是Servlet程序、Filter过滤器、Listener监听器
   - Listener是JavaEE的规范，就是接口
   - 监听器的作用是，监听某种事物的变化。然后通过回调函数，反馈给客户端（程序）去做一些相应的处理

5. ServletContextListener监听器

   - ServletContextListener他可以监听ServletContext对象的创建和销毁

     ServletContext对象在web工程启动的时候创建，在web工程停止的时候销毁

     监听到创建和销毁之后都会分别调用ServletContextListener监听器的方法反馈

   - 如何使用ServletContextListener监听器监听ServletContext对象

     1. 编写一个类去实现ServletContextListener
     2. 实现其两个回调方法
     3. 到web.xml中去配置监听器

# 第八章 EL表达式&JSTL标签库

## 一，EL表达式

1. 什么是EL表达式，有什么作用

   - EL表达式的全称是：Expression Language，是表达式语言
   - EL表达式主要是代替jsp页面中的表达式脚本在jsp页面中进行数据输出，因为EL表达式在输出数据的时候，要比jsp的表达式脚本要简洁很多
   - EL表达式的格式是：${表达式}
   - EL表达式在输出null值的时候，输出的是空串、jsp表达式脚本输出null值的时候，输出的是null字符串

2. EL表达式搜索域数据的顺序

   当四个域中都有相同key的数据的时候，EL表达式会按照四个域的从小到大的顺序去进行搜索，找到就输出

3. EL表达式运算

   - 关系运算

     ![](F:\截图\捕获131.PNG)

   - 逻辑运算

     ![](F:\截图\捕获132.PNG)

   - 算术运算

     ![](F:\截图\捕获133.PNG)

   - empty运算

     empty运算可以判断一个数据是否为空，如果为空，则输出true不为空输出false

     以下几种情况为空

     1. 值为null时为空
     2. 值为空串时为空
     3. 值是Object类型数组，长度为0的时候为空
     4. map集合，元素个数为0为空

   - 三元运算

     表达式1 ? 表达式2 : 表达式3;

   - .点运算和[]中括号运算

     .运算可以输出Bean对象中某个属性的值

     []中括号运算，可以输出有序集合中某个元素的值

     并且[]中括号运算，还可以输出map集合中key里含有特殊字符的key的值

4. EL的11个隐含对象

   ![](F:\截图\捕获134.PNG)

5. EL获取四个特定域中的属性

   pageScope				pageContext域

   requestScope			Request域

   sessionScope			Session域

   applicationScope		ServletContext域

6. pageContext演示

   ![](F:\截图\捕获135.PNG)

## 二，JSTL标签库

1. JSTL标签库全称是JSP Standard Tag Library，JSP标准标签库，是一个不断完善的开放源代码的JSP标签库。

   EL表达式主要是为了替换jsp中的表达式脚本，而标签库则是为替换代码脚本，这样使得整个jsp页面变得更加简洁。

2. ![](F:\截图\捕获136.PNG)

3. core核心库使用

   - ```jsp
     <%--
         <c:set/>
             set标签可以往域中保存数据
             scope属性设置保存到那个域
             page表示PageContext域(默认)
             request表示Request域
             session表示Session域
             application表示ServletContext域
             var属性设置key
             value属性设置值
     --%>
     <c:set scope="page" var="height" value="168"/>
     ${ pageScope.height }
     <%--
         <c:if/>
             if标签用来做if判断
             test属性表示判断的条件(使用EL表达式输出)
     --%>
     <c:if test="${1 == 1}">
         true
     </c:if>
     <%--
         <c:choose> <c:when> <c:otherwise>
             多路判断，和switch...case...default非常接近
             choose标签开始选择判断
             when标签表示每一种判断情况
             test属性表示当前这种判断情况的值
             otherwise表示剩下的情况
     --%>
     <c:choose>
         <c:when test="${pageScope.height > 180}">
             大于180
         </c:when>
         <c:when test="${pageScope.height > 170}">
             大于170
         </c:when>
         <c:otherwise>
             小于170
         </c:otherwise>
     </c:choose>
     <%--
         <c:forEach/>
             遍历输出
             begin属性设置开始的索引
             end属性设置结束的索引
             var属性设置循环的变量
             items属性表示遍历的数据源(遍历的集合)
             step属性表示遍历的步长值
             varStatus属性表示当前遍历到的数据状态
     --%>
     <c:forEach begin="1" end="10" var="i">
         ${i}
     </c:forEach>
     <%
     request.setAttribute("arr", new String[]{"1", "2", "3"});
     %>
     <c:forEach items="${requestScope.arr}" var="item">
         ${item}
     </c:forEach>
     <%
     Map map = new HashMap();
     map.put("key1", "value1");
     map.put("key2", "value2");
     map.put("key3", "value3");
     request.setAttribute("map", map);
     %>
     <c:forEach items="${requestScope.map}" var="entry">
         ${entry.value}
     </c:forEach>
     ```

   - status

     ![](F:\截图\捕获137.PNG)

# 第九章 文件的上传和下载

## 一，文件的上传

1. 文件的上传步骤

   - 要有一个form标签，method=post请求
   - form标签的encType属性值必须为multipart/form-data值
   - 在form标签中使用input type=file添加上传的文件
   - 编写服务器代码（Servlet程序）接收，处理上传的数据
   - ![](F:\截图\捕获138.PNG)

2. commons-fileupload.jar和commons-io.jar常用API介绍

   - ServletFileUpload类，用于解析上传的数据

   - FileItem类，表示每一个表单项

   - boolean ServletFileUpload.isMultipartContent(HttpServletRequest request)

     判断当前上传的数据格式是否是多段的格式

   - public List<FileItem\> parseRequest(HttpServletRequest requset)

     解析上传的数据

   - boolean FileItem.isFormField()

     判断当前这个表单项是否是普通的表单项，还是上传的文件类型

     true表示普通类型的表单项

     false表示上传的文件类型

   - String FileItem.getFieldName()

     获取当前表单项的name属性值

   - String FileItem.getString()

     获取当前表单项的值

   - String FileItem.getName()

     获取上传的文件名

   - void FileItem.write(file)

     将上传文件写到参数file锁所指向的硬盘位置

3. ```java
   //1. 判断当前上传的数据格式是否是多段的格式
   if (ServletFileUpload.isMultipartContent(req)) {
       //2. 创建FileItemFactory实现类对象
       FileItemFactory fileItemFactory = new DiskFileItemFactory();
       //3. 创建ServletFileUpload对象
       ServletFileUpload servletFileUpload = new ServletFileUpload(fileItemFactory);
       try {
           //4. 解析上传的数据
           List<FileItem> fileItems = servletFileUpload.parseRequest(req);
           //5. 判断所有的表单项是否为普通表单项
           for (FileItem fileItem : fileItems) {
               if (fileItem.isFormField()) {
                   //普通表单项，获取表单项的name属性值和表单项的值
                   System.out.println(fileItem.getFieldName() + ":" + fileItem.getString("UTF-8"));
               }else {
                   System.out.println("上传的文件" + fileItem.getName());
                   //上传的文件，写入硬盘
                   fileItem.write(new File("e:\\" + fileItem.getName()));
               }
           }
       } catch (Exception e) {
           e.printStackTrace();
       }
   }
   ```

## 二，文件的下载

1. ```java
   //1. 要下载的文件名
   String downloadFileName = "我的图片.jpg";
   //2. 获取要下载的文件内容(通过ServletContext对象可获取)
   ServletContext servletContext = getServletContext();
   //3. 获取要下载的文件类型
   String mimeType = servletContext.getMimeType("/file/" + downloadFileName);
   //4. 设置ContentType，告诉客户端返回的数据类型
   resp.setContentType(mimeType);
   //5. 设置响应头，告诉客户端收到的数据是用于下载使用
   //      Content-Disposition响应头:表示收到的数据怎么处理
   //      attachment:表示附件，表示用于下载
   //      filename:指定下载的文件名
   //      URLEncoder.encode()把汉字转换成为URL编码
   //      new BASE64Encoder().encode()把汉字转成BASE64编码
   if (req.getHeader("User-Agent").contains("Firefox")) {
       //如果是火狐浏览器，使用BASE64编码
       resp.setHeader("Content-Disposition", "attachment;filename=?UTF-8?B?" + new BASE64Encoder().encode(downloadFileName.getBytes(StandardCharsets.UTF_8)));
   }else {
       //其他使用URL编码
       resp.setHeader("Content-Disposition", "attachment;filename=" + URLEncoder.encode(downloadFileName, "UTF-8"));
   }
   //6. 获取要下载的文件数据
   InputStream resourceAsStream = servletContext.getResourceAsStream("/file/" + downloadFileName);
   //7. 获取响应的输出流
   ServletOutputStream outputStream = resp.getOutputStream();
   //8. 用IOUtils.copy()方法把获取的输入流的文件数据全部复制给输出流，输出给客户端
   IOUtils.copy(resourceAsStream, outputStream);
   }
   ```

# 第十章 Cookie和Session

## 一，Cookie

1. 什么是Cookie

   - Cookie是有服务器通知客户端保存键值对的一种技术
   - 客户端有了Cookie后，每次请求都发送给服务器
   - 每个Cookie的消息大小不能超过4KB

2. Cookie的创建

   - ```java
     protected void createCookie(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
         //1. 创建Cookie对象
         Cookie cookie = new Cookie("key", "value");
         //2. 通知客户端保存Cookie对象
         resp.addCookie(cookie);
         //3. 解决中文乱码
         resp.setContentType("text/html; charset=UTF-8");
         resp.getWriter().write("Cookie对象创建成功");
     }
     ```

3. Cookie的获取

   - ```java
     protected void getCookie(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
         //getCookies()方法返回包含所有cookie的cookie[]
         Cookie[] cookies = req.getCookies();
         resp.setContentType("text/html; charset=UTF-8");
         for (Cookie cookie : cookies) {
             //getName()方法得到key，getValue()方法得到value
             resp.getWriter().write(cookie.getName() + "=" + cookie.getValue() + "<br/>");
         }
         //使用CookieUtils查找需要的cookie
         Cookie key = CookieUtils.findCookie("key", req.getCookies());
         if (key != null) {
             resp.getWriter().write("找到了name为key的cookie值为" + key.getValue());
         }
     }
     ```

4. Cookie值的修改

   - 方案一：
     1. 先创建一个要修改的同名的Cookie对象
     2. 在构造器，同时赋予新的Cookie值
     3. 调用response.addCookie(Cookie);
   - 方案二
     1. 先查找到需要修改Cookie对象
     2. 调用setValue()方法赋予新的Cookie值
     3. 调用response.addCookie(Cookie)通知客户端修改

5. Cookie生命控制

   - Cookie的生命控制是指如何管理Cookie什么时候销毁（删除）
   - setMaxAge()
     - 正数，表示在指定的秒数后过期
     - 负数，表示浏览器一关，Cookie就会被删除（默认值是-1）
     - 零，表示马上删除Cookie

6. Cookie有效路径Path的设置

   - Cookie的path属性可以有效的过滤那些Cookie可以发送给服务器，那些不发，path属性是通过请求的地址来进行有效的过滤

   - CookieA          path=/工程路径

     CookieB          path=/工程路径/abc

     http://ip:port/工程路径

     CookieA发送，CookieB不发送

     http://ip:port/工程路径/abc

     CookieA发送，CookieB发送

## 二，Session会话

1. 什么是Session会话

   - Session就是一个接口（HttpSession）
   - Session就是会话，他是用来维护客户端和服务器之间关联的一种技术
   - 每个客户端都有自己的一个Session会话
   - Session会话中我们经常用来保存用户登录之后的信息

2. 如何创建Session和获取id号

   - request.getSession()

     ==第一次调用是创建Session会话==

     ==之后调用都是获取前面创建好的Session会话对象==

   - isNew()判断是不是刚创建出来的

     true表示刚创建

     false表示获取之前创建

   - 每个会话都有一个身份证号，也就是id值，而且这个id是唯一的

     getId()得到Session的会话id值

   - ```java
     protected void createAndGetSession(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
         //创建或获取Session
         HttpSession session = req.getSession();
         //判断是否为new
         boolean sessionNew = session.isNew();
         //获取id值
         String id = session.getId();
         resp.getWriter().write("当前Session的id是:" + id + "<br/>");
         resp.getWriter().write("是否为new?" + sessionNew + "<br/>");
     }
     ```

3. Session域数据的存取

   - setAttribute()
   - getAttribute()

4. Session生命周期控制

   - ==void setMaxInactiveInterval(int interval)  设置Session的超时时间（以秒为单位），超过指定的时长Session就会被销毁==

     值为正数时：设定Session超时时长

     值为负数时：永不超时

   - ==int getMaxInactiveInterval()  获取Session的超时时间==

   - ==void Invalidate()  让当前Session会话马上超时无效==

   - Session默认的超时时间为30分钟，在Tomcat服务器的配置文件web.xml中默认有以下配置，他表示配置了当前Tomcat服务器下所有的Session超时默认时长为30分钟

     ```xml
     <session-config>
         <session-timeout>30</session-timeout>
     </session-config>
     ```

     你可以在你自己的web.xml配置文件中做以上相同配置，就可以修改你的web工程所有Session的默认超时时长

5. 浏览器和Session之间的关联的技术内幕

   Session技术底层其实是基于Cookie技术来实现的

   ![](F:\截图\捕获139.PNG)

# 第十一章 Filter过滤器

## 一，什么是Filter过滤器

1. Filter过滤器是javaweb的三大组件之一，三大组件分别是，Servlet程序、Listener监听器、Filter过滤器
2. Filter过滤器是javaEE的规范，也就是接口
3. Filter过滤器作用是，拦截请求，过滤响应
4. 拦截请求常见的应用场景有
   - 权限检查
   - 日记操作
   - 事务管理
   - 。。。

## 二，Filter基本使用步骤

1. 编写一个类去实现Filter接口

2. 实现过滤方法doFilter()

3. 到web.xml中去配置拦截路径

4. ```xml
   <!--配置filter-->
   <filter>
       <!--给filter起一个别名-->
       <filter-name>FilterServlet</filter-name>
       <!--配置filter全类名-->
       <filter-class>com.wgx.filter.FilterServlet</filter-class>
   </filter>
   <!--配置拦截路径-->
   <filter-mapping>
       <!--filter-name表示给当前拦截路径给那个filter使用-->
       <filter-name>FilterServlet</filter-name>
       <!--filter-pattern表示拦截路径
               /   表示请求地址为http://ip:port/工程路径/     映射到IDEA的web目录下
               /admin/*    表示http://ip:port/工程路径/admin/下的全部-->
       <url-pattern>/admin/*</url-pattern>
   </filter-mapping>
   ```

5. ```java
   public class FilterServlet implements Filter {
       @Override
       public void init(FilterConfig filterConfig) throws ServletException {
           Filter.super.init(filterConfig);
       }
   
       @Override
       public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
           //类型转换
           HttpServletRequest request =  (HttpServletRequest) servletRequest;
           //获取LoginUser
           Object loginUser = request.getSession().getAttribute("loginUser");
           if (loginUser == null) {
               //没有登录跳转到登录页面
               request.getRequestDispatcher("/login.html").forward(servletRequest, servletResponse);
               return;
           }else {
               //已经登录,允许访问
               filterChain.doFilter(servletRequest, servletResponse);
           }
       }
   
       @Override
       public void destroy() {
           Filter.super.destroy();
       }
   }
   ```

## 三，Filter的生命周期

1. 构造器方法

2. init初始化方法

   ---

   第一二步在web工程启动的时候执行（Filter已经创建）

3. doFilter过滤方法

   ---

   第三步每次拦截到请求都会执行

4. destroy销毁

   ---

   第四步停止web工程的时候就会执行（停止web工程也会销毁Filter过滤器）

## 四，FilterConfig类

FilterConfig类也是Filter过滤器的配置文件类

Tomcat每次创建Filter的时候，也会同时创建一个FilterConfig类，这里包含了Filter配置文件的配置信息

FilterConfig类的作用是获取filter过滤器配置内容

1. 获取Filter的名称filter-name的内容
2. 获取在Filter中配置的init-param初始化
3. 获取ServletContext对象

## 五，FilterChain

![](F:\截图\捕获140.PNG)

## 六，Filter过滤器的拦截路径

1. 精确匹配

   \<url-pattern>/target.jsp<\\url-pattern>

   以上配置路径，表示请求地址必须为：http://ip:port/工程路径/target.jsp

2. 目录匹配

   \<url-pattern>/admin/*<\\url-pattern>

   以上配置的路径，表示请求地址必须为：http://ip:prot/工程路径/admin/*

3. 后缀名匹配

   \<url-pattern>*.html<\\url-pattern>

   以上配置的路径，表示必须以html结尾才会拦截到

4. Filter过滤器只关心请求的地址是否匹配，不管性请求的资源是否存在

## 七，ThreadLocal的使用

1. TreadLocal的作用他可以解决多线程的数据安全问题

2. TreadLocal他可以给当前线程关联一个数据（可以是普通变量，可以是对象，也可以是数组，集合）

3. TreadLocal的特点

   - TreadLocal可以为当前线程关联一个数据（他可以像Map一样存取数据，key为当前线程）
   - 每一个TreadLocal对象只能为当前线程关联一个数据，如果要为当前线程关联多个数据，就需要使用多个TreadLocal对象实例
   - 每个TreadLocal对象实例定义的时候，一般是static类型
   - TreadLocal中保存数据，在线程销毁后，由JVM自动释放

4. Tomcat同一配置错误页面

   ```xml
   <!--error-page标签配置服务器出错之后，自动跳转的页面-->
   <error-page>
       <!--error-code是错误类型-->
       <error-code>500</error-code>
       <!--location标签表示要跳转去的页面路径-->
       <location>/pages/error/error500.jsp</location>
   </error-page>
   ```

# 第十二章 JSON、AJAX、i18n

## 一，JSON

1. 什么是JSON

   JSON（JavaScript Object Notation）是一种轻量级的数据交换格式，易于人阅读和编写，同时也易于机器解析和生成。JSON采用完全独立于语言的文本格式，而且很多语言都提供了对JSON的支持（包括C,C++,Java,JavaScript,Perl,Python等），这就使得JSON成为理想的数据交换格式

2. JSON的定义

   json是由键值对组成，并且有大括号包围。每个键由引号引起来，键和值之间使用冒号进行分隔，多组键值对之间进行逗号分隔

3. JSON的访问

   - json本身是一个对象
   - json中的key我们可以理解是对象中的一个属性
   - json中的key访问就和访问对象的属性一样，json对象.key

4. JSON常用的两个方法

   - json的存在有两种形式

     一种是对象的形式存在，json对象

     一种是字符串的形式存在，json字符串

     一般在操作json中的数据的时候，需要json对象格式

     一般在客户端和服务器之间进行数据交换的时候，需要json字符串

   - JSON.stringify()  把json对象转换成为json字符串

   - JSON.parse()      把json字符串转换成为json对象

5. Javabean和JSON的相互转换

   ```java
   public static void main(String[] args) {
       Person wgx = new Person("wgx", 22);
       Gson gson = new Gson();
       String personJsonString = gson.toJson(wgx);
       System.out.println(personJsonString);//{"name":"wgx","age":22}
       Person person = gson.fromJson(personJsonString, Person.class);
       System.out.println(person);//Person{name='wgx', age=22}
   }
   ```

6. List和JSON的相互转换

   ```java
   public class PersonListType extends TypeToken<ArrayList<Person>> {
   }
   
   public static void main(String[] args) {
       List<Person> personList = new ArrayList<>();
       personList.add(new Person("wgx", 22));
       personList.add(new Person("WGX", 22));
       Gson gson = new Gson();
       String personListJsonString = gson.toJson(personList);
       System.out.println(personListJsonString);//[{"name":"wgx","age":22},{"name":"WGX","age":22}]
       List<Person> list = gson.fromJson(personListJsonString, new PersonListType().getType());
       System.out.println(list);//[Person{name='wgx', age=22}, Person{name='WGX', age=22}]
       for (Person person : list) {
           System.out.println(person);
       }//Person{name='wgx', age=22}
       //Person{name='WGX', age=22}
   }
   ```

7. Map和JSON的相互转换

   ```java
   public static void main(String[] args) {
       Map<Integer, Person> personHashMap = new HashMap<>();
       personHashMap.put(1, new Person("wgx", 22));
       personHashMap.put(2, new Person("WGX", 22));
       Gson gson = new Gson();
       String personMapJsonString = gson.toJson(personHashMap);
       System.out.println(personMapJsonString);//{"1":{"name":"wgx","age":22},"2":{"name":"WGX","age":22}}
       Map<Integer, Person> map = gson.fromJson(personMapJsonString, new TypeToken<HashMap<Integer, Person>>() {}.getType());//匿名内部类
       System.out.println(map);//{1=Person{name='wgx', age=22}, 2=Person{name='WGX', age=22}}
       for (Person person : map.values()) {
           System.out.println(person);
       }//Person{name='wgx', age=22}
       //Person{name='WGX', age=22}
   }
   ```

## 二，AJAX请求

1. 什么是AJAX

   AJAX（Asynchronous Javascript And XML）异步JavaScript和XML，是指一种创建交互式网页应用开发技术

   ajax是一种浏览器通过js异步发起请求，局部更新页面的技术 

   局部更新浏览器地址栏不会发生变化，不会舍弃原来的页面内容

2. 原生JavaScript的AJAX请求示例

   ```javascript
   <script>
       function ajaxRequest() {
       //1. 创建XMLHttpRequest
       var xmlHttpRequest = new XMLHttpRequest();
       //2. 调用open方法设置请求参数
       xmlHttpRequest.open("GET", "http://localhost:8080/JSON_AJAX_i18n/ajaxServlet?action=javaScriptAjax", true);
       //4. 在send方法前绑定onreadystatechange事件，处理请求完成后的操作
       xmlHttpRequest.onreadystatechange = function () {
           if (xmlHttpRequest.readyState == 4 && xmlHttpRequest.status == 200) {
               //把响应的数据显示在页面上
               var person = JSON.parse(xmlHttpRequest.responseText);
               document.getElementById("div001").innerHTML = "姓名:" + person.name + ",年龄:" + person.age;
           }
       }
       //3. 调用send方法发送请求
       xmlHttpRequest.send();
   }
   </script>
   ```

   ```java
   protected void javaScriptAjax(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
       System.out.println("javaScriptAjax收到AJAX请求");
       Gson gson = new Gson();
       String json = gson.toJson(new Person("吴功勋", 22));
       resp.setContentType("text/html; charset=UTF-8");
       resp.getWriter().write(json);
   }
   ```

3. jQuery中的AJAX请求

   - ajax方法

     url		表示请求的地址

     type	 表示请求的类型GET或POST请求

     data	 表示发送给服务器的数据

     ​					格式有两种：

     ​							一，name=value&name=value

     ​							二，{key:value}

     success  	 请求成功，响应的回调函数

     dataType	响应的数据类型

     ​						常用的数据类型：

     ​							一，text表示纯文本

     ​							二，xml表示xml数据

     ​							三，json表示json对象

     ```javascript
     <script>
         $(function () {
         $("#ajax").click(function () {
             $.ajax({
                 url: "http://localhost:8080/JSON_AJAX_i18n/ajaxServlet",
                 data: "action=jQueryAjax",
                 type: "GET",
                 success: function (data) {
                     $("#message").html("name:" + data.name + ",age:" + data.age);
                 },
                 dataType: "json"
             });
         });
     });
     </script>
     ```

     ```java
     protected void jQueryAjax(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
         System.out.println("jQueryAjax收到AJAX请求");
         Gson gson = new Gson();
         String json = gson.toJson(new Person("吴功勋", 22));
         resp.setContentType("text/html; charset=UTF-8");
         resp.getWriter().write(json);
     }
     ```

   - get方法和post方法

     url		请求的地址

     data	 发送给服务器的数据

     callback		成功的回调函数

     type		返回的数据类型

   - getJSON

     url		请求的地址

     data	 发送给服务器的数据

     callback		成功的回调函数

   - 表单序列化serialize()

     serialize()可以把表单中所有表单项的内容获取到，并以name=value&name=value的形式进行拼接

