# 一，CSS基础选择器

基础选择器

![](F:\截图\屏幕截图 2022-09-28 151321.png)

# 二，字体属性

1. font-family

   如果第一种字体浏览器没有安装，就依次使用后面的，都没有就使用系统默认

   ```css
   font-family: "微软雅黑", "Times New Roman";
   ```

2. font-size

   标题标签需要单独指定大小

   ```css
   font-size: 30px;
   ```

3. font-weight

   ```css
   /*font-weight: bold;*/
   font-weight: 700;
   /*100-900之间，400等于normal，700等于bold*/
   ```

4. font-style

   ```css
   font-style: italic;
   ```

5. font复合属性写法

   font: font-style font-variant font-weight font-size/line-height font-family

   不能修改顺序，必须保留font-size和font-family

   ```css
   font: italic 700 30px "微软雅黑";
   ```

# 三，文本属性

1. color

   ```css
   /*color: blue;*/
   /*color: #0037ff;*/
   color: rgba(0, 34, 255, 0.99);
   ```

2. text-align

   ```css
   text-align: center;
   ```

3. text-decoration（文本装饰）

   ![](F:\截图\屏幕截图 2022-09-28 153743.png)

4. text-indent（文本缩进）

   ```css
   /*text-indent: 20px;*/
   text-indent: 2em;
   /*2em:当前元素两个文字大小的距离*/
   ```

5. line-height（行间距）

   ![](F:\截图\屏幕截图 2022-09-28 154420.png)

   ```css
   line-height: 20px;
   ```

# 四，复合选择器

1. 后代选择器

   ```css
   ol li {
      color: blue;
   }
   ```

2. 子选择器

   子选择器只能选择作为某元素的最近的一级子元素

   ```css
   ol > li {
      color: red;
   }
   ```

3. 并集选择器

   并集选择器可以选择多组标签，同时为他们定义相同样式

   ```css
   p, div {
      color: yellow;
   }
   ```

4. 链接伪类选择器

   ![](F:\截图\屏幕截图 2022-09-28 162424.png)

   ```css
   a:link {
      color: blue;
   }
   a:visited {
      color: gray;
   }
   a:hover {
      color: red;
   }
   a:active {
      color: green;
   }
   /*尽量按照link-visited-hover-active顺序*/
   ```

5. :focus伪类选择器

   :focus伪类选择器用于选取获取焦点的表单元素

   ```css
   input:focus {
      background-color: skyblue;
   }
   ```

# 五，CSS的元素显示模式

1. 块元素

   常见的块元素有<h1\>~<h6\>、<p\>、<div\>、<ul\>、<ol\>、<li\>等，其中<div\>标签是最典型的块元素。

   块级元素的特点：

   - 比较霸道，自己独占一行。
   - 高度，宽度、外边距以及内边距都可以控制
   - 宽度默认是容器（父级宽度）的100%
   - 是一个容器及盒子，里面可以放行内或者块级元素。

2. 行内元素

   常见的行内元素有<a\>、<strong\>、<b\>、<em\>、<i\>、<del\>、<s\>、<ins\>、<u\>、<span\>等，其中<span\>标签是最典型的行内元素。有的地方也将行内元素称为内联元素。

   行内元素的特点：

   - 相邻行内元素在一行上，一行可以显示多个。
   - 高、宽直接设置是无效的
   - 默认宽度就是它本身内容的宽度
   - 行内元素只能容纳文本或其他行内元素。
   - 特殊情况链接<a\>里面可以放块级元素，但是最好给<a\>转换一个块模式

3. 行内块元素

   在行内元素中有几个特殊的标签<img/\>、<input/\>、<td\>,它们同时惧有块元素和行内元素的特点。有些资料称它们为行内块元素。

   行内块元素的特点：

   - 和相邻行内元素（行内块）在一行上，但是他们之间会有空白缝隙。一行可以显示多个（行内元素特点）。
   - 默认宽度就是它本身内容的宽度（行内元素特点）。
   - 高度，行高、外边距以及内边距都可以控制（块级元素特点）。

4. 显示模式的转换

   - 转换为块元素：display: block;
   - 转换为行内元素：display: inline;
   - 转换为行内块元素：display: inline-block;

# 六，背景

1. 背景颜色

   默认值为transparent（透明度）

   ```css
   background-color: skyblue;
   ```

2. 背景图片

   ```css
   background-image: url("baidu.png");
   ```

3. 背景平铺

   默认情况下平铺

   ```css
   background-repeat: no-repeat;
   /*background-repeat: repeat-x;沿着x轴平铺*/
   /*background-repeat: repeat-y;沿着y轴平铺*/
   ```

4. 背景图片的位置

   ```css
   /*方向单位*/
   background-position: center center;
   /*精确单位*/
   background-position: 20px 20px;
   ```

5. 背景固定

   ```css
   /*背景图像是随对象内容滚动*/
   background-attachment: scroll;
   /*背景图像固定*/
   background-attachment: fixed;
   ```

6. 背景属性复合写法

   ```css
   background: url("111.jpg") no-repeat center center fixed;
   ```

7. 背景色半透明

   ```css
   /*50%为透明度，或者0.5*/
   background-color: rgba(0, 0, 0, 50%);
   ```

# 七，盒子模型

1. 盒子模型（Box Model）的组成

   - border边框
   - content内容
   - padding内边距
   - margin外边距
   - ![](F:\截图\屏幕截图 2022-09-29 151351.png)

2. 边框

   ```css
   border-width: 5px;
   /*dashed:虚线，solid:实线，dotted:点线*/
   border-style: solid;
   border-color: blue;
   ```

   复合写法，没有顺序

   ```css
   border: 5px solid blue;
   ```

   分别设置4个方向的边框

   ```css
   border-top: 5px solid yellow;
   border-left: 5px solid red;
   ```

   合并相邻的边框

   ```css
   border-collapse: collapse;
   ```

3. 内边距

   ![](F:\截图\屏幕截图 2022-09-29 152713.png)

   复合写法

   ![](F:\截图\屏幕截图 2022-09-29 152907.png)

4. 外边距

   ![](F:\截图\屏幕截图 2022-09-29 155642.png)

5. 块级盒子水平居中

   外边距可以让盒子水平居中，但是必须满足两个条件

   - 盒子必须制定宽度

   - 盒子左右的外边距都设置为auto

   - ```css
     margin: 10px auto;
     ```

6. 行内元素或者行内块元素水平居中可以直接给父元素添加text-align: center

7. 外边距合并

   当上下相邻的两个块元素（兄弟关系）相遇时，如果上面的元素有下外边距margin-bottom,下面的元素有上外边距margin-top,则他们之间的垂直间距不是margin-bottom与margin-top之和。取两个值中的较大者这种现象被称为相邻块元素垂直外边距的合并。

8. 嵌套块元素垂直外边距的塌陷

   对于两个嵌套关系（父子关系）的块元素，父元素有上外边距同时子元素也有上外边距，此时父元素会塌陷较大的外边距值。

   解决方法：

   - 可以为父元素定义上边框
   - 可以为父元素定义上内边距
   - 可以为父元素添加overflow:hidden

9. 清除内外边距

   ```css
   * {
      padding: 0;
      margin: 0;
   }
   ```

10. 圆角边框

    ```css
    border-radius: 90px;
    ```

    ![](F:\截图\屏幕截图 2022-09-29 172417.png)

    - 圆形的做法

      ```css
      border-radius: 50%;/*高度或者宽度的一半*/
      ```

    - 圆角矩形的做法

      ```css
      border-radius: 50px;/*高度的一半*/
      ```

    - 可以写四个值，分别代表左上角，右上角，右下角，左下角

      ```css
      border-radius: 10px 20px 30px 40px;
      ```

11. 盒子阴影

    ![](F:\截图\屏幕截图 2022-09-29 173206.png)

12. 文字阴影

    ![](F:\截图\屏幕截图 2022-09-29 173650.png)

# 八，浮动

1. 传统网页布局的三种方式

   - 标准流：就是标签按照规定好默认方式排列
   - 浮动
   - 定位

2. 什么是浮动

   float属性用于创建浮动框，将其移动到一边，直到左边缘或者右边缘触及包含块或者另一个浮动框的边缘

   ![](F:\截图\屏幕截图 2022-09-29 174842.png)

3. 浮动特性

   - 浮动元素会脱离标准流（脱标）
   - 浮动的元素会一行内显示并且元素顶部对齐
   - 浮动的元素会具有行内块元素的特性

4. 清除浮动

   - 额外标签法

     在浮动元素末尾添加一个空标签<div class="clear"\></div\>

     ```css
     .clear {
        clear: both;
     }
     ```

   - 父级添加overflow属性

     给父元素添加overflow: hidden;或者auto，scroll

   - 父级添加after伪元素

     给父元素添加类

     ```css
     .clearfix:after {
        content: "";
        display: block;
        height: 0;
        clear: both;
        visibility: hidden;
     }
     .clearfix {
        /*兼容IE6，IE7*/
        *zoom: 1;
     }
     ```

   - 父级添加双伪元素

     给父元素添加类

     ```css
     .clearfix:before, .clearfix:after {
        content: "";
        display: table;
     }
     .clearfix:after {
        clear: both;
     }
     .clearfix {
        *zoom: 1;
     }
     ```

# 九，CSS属性书写顺序

![](F:\截图\屏幕截图 2022-09-30 164845.png)

# 十，定位

1. 定位的组成

   定位：将盒子定在某一个位置，按照定位的方式移动盒子

   定位=定位模式+边偏移

   定位模式用于指定一个元素在文档中的定位方式。边偏移则决定了该元素的最终位置

2. 定位模式

   定位模式决定元素的定位方式，它通过CSS的position属性来设置，其值可以分为四个：

   ![](F:\截图\屏幕截图 2022-10-02 204122.png)

3. 边偏移

   边偏移就是定位的盒子移动到最终位置。有top、bottom、Ieft和right4个属性。

   ![](F:\截图\屏幕截图 2022-10-02 204220.png)

4. 静态定位

   静态定位就是元素的默认定位方式，无定位的意思

   ```css
   position: static;
   ```

   - 静态定位按照标准流特性摆放，没有边偏移

5. 相对定位

   相对定位是元素在移动位置的时候，是相对于他原来的位置来说的

   ```css
   position: relative;
   ```

   - 它是相对于自己原来的位置来移动的（移动位置的时候参照点是自己原来的位置）。
   - 原来在标准流的位置继续占有，后面的盒子仍然以标准流的方式对待它。（不脱标，继续保留原来位置）

6. 绝对定位

   绝对定位是元素在移动位置的时候，是相对于他祖先元素来说的

   ```css
   position: absolute;
   ```

   - 如果没有祖先元素或者祖先元素没有定位，则以浏览器为准定位(Document文档)。
   - 如果祖先元素有定位（相对、绝对、固定定位），则以最近一级的有定位祖先元素为参考点移动位置。
   - 绝对定位不再占用原来的位置（脱标）

7. 固定定位

   固定定位是元素固定于浏览器可视区的位置。主要使用场景：可以在浏览器页面滚动时元素的位置不会改变。

   ```css
   position: fixed;
   ```

   - 跟父元素没有关系
   - 不随着滚动条滚动
   - 不占有原先的位置（脱标）

8. 粘性定位

   粘性定位可以被认为是相对定位和固定定位的混合。

   ```css
   position: sticky;
   ```

   - 以浏览器的可视窗口为参照点移动元素（固定定位特点）
   - 粘性定位占有原先的位置（相对定位特点）

9. 定位的总结

   ![](F:\截图\屏幕截图 2022-10-02 221213.png)

10. 定位的叠放次序

    在使用定位布局时，可能会出现盒子重虽的情况。此时，可以使用z-index来控制盒子的前后次序(z轴)

    ```css
    z-index: 1;
    ```

    - 数值可以是正整数、负整数或0，默认是auto,数值越大，盒子越靠上
    - 如果属性值相同，则按照书写顺序，后来居上
    - 数字后面不能加单位
    - 只有定位的盒子才有Z-index属性

11. 定位的浮动特殊性

    绝对定位和固定定位也和浮动类似。

    - 行内元素添加绝对或者固定定位，可以直接设置高度和宽度。
    - 块级元素添加绝对或者固定定位，如果不给宽度或者高度，默认大小是内容的大小。

    - ==脱离标准流的的元素不会触发外边距合并问题==
    - ==绝对定位（固定定位）会完全压住盒子==，浮动元素不同，只会压住它下面标准流的盒子，但是不会压住下面标准流盒子里面的文字（图片）但是绝对定位（固定定位）会压住下面标准流所有的内容。==浮动之所以不会压住文字，因为浮动产生的目的最初是为了做文字环绕效果的。==文字会围绕浮动元素

# 十一，元素的显示与隐藏

1. display

   - display: none;隐藏元素
   - display: block;显示元素
   - display隐藏的元素后，不再占用原来的位置

2. visibility

   - visibility: visiable;元素可见
   - visibility: hidden;元素隐藏
   - visibility隐藏元素后，继续占有原来的位置

3. overflow

   ![](F:\截图\屏幕截图 2022-10-03 170404.png)

# 十二，精灵图

1. 原理

   - 精灵图主要针对于小的背景图片使用。
   - 主要借助于背景位置来实现（background-position）
   - 一般情况下精灵图都是负值。（千万注意网页中的坐标：轴右边走是正值，左边走是负值，y轴同理。）

2. 使用

   ```css
   background: url("sprites.png") no-repeat -182px 0;
   ```

# 十三，字体图标

1. 字体图标的优点

   - 轻量级：一个图标字体要比一系列的图像要小。一旦字体加载了，图标就会马上渲染出来，减少了服务器请求
   - 灵活性：本质其实是文字，可以很随意的改变颜色、产生阴影、透明效果、旋转等
   - 兼容性：几乎支持所有的浏览器，请放心使用

   注意：字体图标不能替代精灵技术，只是对工作中图标部分技术的提升和优化。

2. 字体图标的下载

   - icomoon字库：http://icomoon.io
   - 阿里iconfont字库：http://www.iconfont.cn/

3. 字体图标的使用

   - 解压icomoon.zip

   - 将font文件夹放到页面根目录下

   - 字体声明

     ```css
     @font-face {
        font-family: 'icomoon';
        src:  url('fonts/icomoon.eot?qxew05');
        src:  url('fonts/icomoon.eot?qxew05#iefix') format('embedded-opentype'),
        url('fonts/icomoon.ttf?qxew05') format('truetype'),
        url('fonts/icomoon.woff?qxew05') format('woff'),
        url('fonts/icomoon.svg?qxew05#icomoon') format('svg');
        font-weight: normal;
        font-style: normal;
        font-display: block;
     }
     ```

   - 打开demo.html复制需要的图标到相应的位置

   - 添加css样式

     ```css
     .iconfont {
        font-family: 'icomoon';
        font-size: 50px;
     }
     ```

4. 字体图标的追加

   把压缩包里面的selection.json重新上传，然后选中自己需要的新图标，重新下载压缩包，替换原来的文件

# 十四，CSS三角

```css
.triangle {
   width: 0;
   height: 0;
   border: 50px solid transparent;
   border-top-color: #000;
}
```

# 十五，CSS用户界面样式

1. 鼠标样式

   ```css
   cursor: pointer;
   ```

   ![](F:\截图\屏幕截图 2022-10-03 204528.png)

2. 取消表单轮廓线

   ```css
   outline: none;
   ```

3. 阻止拖拽文本域

   ```css
   resize: none;
   ```

# 十六，vertical-align属性

用于设置一个元素的垂直对齐方式，但是它只针对于行内元素或者行内块元素有效。

![](F:\截图\屏幕截图 2022-10-03 205358.png)

![](F:\截图\屏幕截图 2022-10-03 205440.png)

# 十七，溢出文字省略号显示

1. 单行文本溢出显示省略号

   - 先强制一行内显示文本

     ```css
     white-space: nowrap;
     ```

   - 超出部分隐藏

     ```css
     overflow: hidden;
     ```

   - 文字用省略号代替超出部分

     ```css
     text-overflow: ellipsis;
     ```

2. 多行文字溢出显示省略号

   多行文本谥出显示省略号，有较大兼容性问题，适合于webKit浏览器或移动端（移动端大部分是webkit内核）

   - overflow: hidden;

   - text-overflow: ellipsis;

   - 弹性伸缩盒子模型显示

     ```css
     display: -webkit-box;
     ```

   - 限制在一个块元素显示的文本的行数

     ```css
     -webkit-line-clamp: 2;
     ```

   - 设置或检索伸缩盒对象的子元素的排列方式

     ```css
     -webkit-box-orient: vertical;
     ```

# 十八，常见布局技巧

1. margin负值的运用

   让边框重叠

   ```css
   border: red 1px solid;
   margin-left: -1px;
   ```

   边框重叠之后添加鼠标经过的效果

   ```css
   ul li:hover {
      /*如果没有定位可以添加相对定位*/
      position: relative;
      /*如果有定位可以使用z-index*/
      z-index: 1;
      border: orange 2px solid;
   }
   ```

2. 三角强化（直角三角形）

   ```css
   width: 0;
   height: 0;
   border-color: transparent red transparent transparent;
   border-style: solid;
   border-width: 40px 20px 0 0;
   ```

# 十九，CSS初始化

```css
/*内外边距清零*/
* {
   margin: 0;
   padding: 0;
}
/*让em和i的文字不倾斜*/
em, i {
   font-style: normal;
}
/*去掉li的小圆点*/
li {
   list-style: none;
}
img {
   /*低版本浏览器如果图片包含了链接外面会有边框的问题*/
   border: 0;
   /*取消图片底侧有空白缝隙*/
   vertical-align: middle;
}
/*鼠标经过button的时候，变成小手*/
button {
   cursor: pointer;
}
/*修改所有链接的颜色和取消下划线*/
a {
   color: #666;
   text-decoration: none;
}
body {
   /*抗锯齿形，让文字显示更清晰*/
   -webkit-font-smoothing: antialiased;
   background-color: #fff;
   font: 12px/1.5 "Microsoft YaHei";
   color: #666;
}
/*清除浮动*/
.clearfix::after {
   visibility: hidden;
   clear: both;
   display: block;
   content: ".";
   height: 0;
}
.clearfix {
   *zoom: 1;
}
```

# 二十，HTML5新特性

1. HTML5新增的语义化标签

   - <header\>：头部标签
   - <nav\>：导航标签
   - <article\>：内容标签
   - <section\>：定义文档某个区域
   - <aside\>：侧边栏标签
   - <footer\>：尾部标签

2. HTML5新增的多媒体标签

   - <audio\>：音频

     ```html
     <audio src="文件地址" controls="controls"></audio>
     ```

     ![](F:\截图\屏幕截图 2022-10-04 165256.png)

   - <video\>：视频

     ```html
     <video src="文件地址" controls="controls"></video>
     ```

     ![](F:\截图\屏幕截图 2022-10-04 164732.png)

3. HTML5新增的input类型

   ![](F:\截图\屏幕截图 2022-10-04 165649.png)

4. HTML5新增的input属性

   ![](F:\截图\屏幕截图 2022-10-04 170308.png)

# 二十一，CSS3新特性

1. 新增选择器

   - 属性选择器

     ![](F:\截图\屏幕截图 2022-10-04 171127.png)

   - 结构伪类选择器

     ![](F:\截图\屏幕截图 2022-10-04 171423.png)

     **nth-child(n)，n可以是数字、关键字和公式。even偶数，odd奇数；2n偶数，2n+1奇数**

   - 伪元素选择器

     伪元素选择器可以帮助我们利用CSS创建新标签元素，而不需要HTML标签，从而简化HTM结构。

     - before和after创建一个元素，但是属于行内元素
     - 新创建的这个元素在文档树中是找不到的，所以我们称为伪元素
     - 语法：element::before
     - before和after必须有content属性
     - before在父元素内容的前面创建元素，after在父元素内容的后面插入元素
     - 伪元素选择器和标签选择器一样，权重为1

     ![](F:\截图\屏幕截图 2022-10-04 202045.png)

2. 盒子模型

   CSS3中可以通过box-sizing来指定盒模型，这样计算盒子大小的方式就会改变

   - box-sizing: content-box;盒子大小为width+padding+border（默认）
   - box-sizing: border-box;盒子大小为width

   如果盒子模型改为了box-sizing:border-box，那padding和border就不会撑大盒子了（前提padding和border不会超过width宽度)

3. 图片变模糊

   filter CSS属性将模糊或颜色偏移等图形效果应用于元素。

   ```css
   filter: 函数();
   /*例如filter: blur(5px);blur模糊处理，数值越大越模糊*/
   ```

4. calc函数

   calc()此CSS函数让你在声明CSS属性值时执行一些计算。

   ```css
   width: calc(100% - 80px);
   ```

5. CSS3过渡

   过渡(transition)是CSS3中具有颜覆性的特征之一，我们可以在不使用Flash动画或JavaScript的情况下，当元素从一种样式变换为另一种样式时为元素添加效果。

   过渡动画：是从一个状态渐渐的过渡到另外一个状态

   现在经常和：hover一起搭配使用。

   ```css
   transition: 要过渡的属性 花费的时间 运动曲线 何时开始;
   ```

   - 属性想要变化的css属性，高度宽度，背景颜色，内外边距都可以。如果需要所有的属性都变化过度，写一个all即可

   - 花费时间：单位是秒（必须写单位）

   - 运动曲线：默认是ease

     ![](F:\截图\屏幕截图 2022-10-04 210348.png)

   - 何时开始：单位是秒（必须写单位）可以设置延迟触发时间，默认是0s

# 二十二，2D转换

1. 移动translate

   2D移动可以改变元素在页面中的位置，类似定位

   ```css
   transform: translate(x, y);
   /*或者分开写*/
   transform: translateX(n);
   transform: translateY(n);
   ```

   - 定义2D转换中的移动，沿着X和Y轴移动元素
   - ==translate不会影响其他元素的位置==
   - translate中的百分比单位是相对于自身元素的translate(50%, 50%);
   - 对行内标签没有效果

2. 旋转rotate

   2D旋转指的是让元素在2维平面内顺时针旋转或者逆时针旋转。

   ```css
   transform: rotate(度数);
   ```

   - rotate里面跟度数，单位是deg比如rotate(45deg)
   - 角度为正时顺时针，负时为逆时针
   - 默认旋转的中心点是元素的中心点

3. 2D转换中心点

   ```css
   transform-origin: x y;
   ```

   - x，y默认转换的中心点是元素的中心点（50%，50%）
   - 还可以给x和y设置像素或者方位名词（top，bottom，left，right，center）

4. 缩放scale

   可以使元素放大或者缩小

   ```css
   transform: scale(x, y);
   ```

   - transform: scale(2, 2);宽高都放大两倍
   - transform: scale(2);相当于transform: scale(2, 2);
   - transform: scale(0.5, 0.5);宽高都缩小0.5
   - 可以设置转换中心点，默认中心点缩放，而且不影响其他盒子

5. 2D转换综合写法

   - 同时使用多个转换格式为：transform: translate(x, y) rotate(度数) scale(x, y)...
   - 当同时有位移和其他属性的时候，要将位移放到最前面

# 二十三，动画

1. 动画的基本使用

   - 定义动画

     ```css
     @keyframes 动画名称 {
         0% {
             width: 100px;
         }
         100% {
             width: 200px
         }
     }
     ```

     - 0%是动画的开始，100%是动画的完成。这样的规则就是动画序列。
     - 在@keyframes中规定某项CSS样式，就能创建由当前样式逐渐改为新样式的动画效果。
     - 动画是使元素从一种样式逐渐变化为另一种样式的效果。您可以改变任意多的样式任意多的次数。
     - 请用百分比来规定变化发生的时间，或用关键词”from”和“to”，等同于0%和100%。

   - 元素使用动画

     ```css
     div {
         animation-name: 动画名称;
         animation-duration: 持续时间;
     }
     ```

2. 动画常用属性

   ![](F:\截图\屏幕截图 2022-10-05 210624.png)

3. 动画简写属性

   animation: 动画名称 持续时间 运动曲线 何时开始 播放次数 是否反方向 动画起始或者结束的状态;

4. 速度曲线细节

   ![](F:\截图\屏幕截图 2022-10-07 132428.png)

   steps()就是分几步来完成动画

# 二十四，3D转换

1. 三维坐标系

   - x轴：水平向右，右边是正值，左边是负值
   - y轴：垂直向下，下面是正值，上面是负值
   - z轴：垂直屏幕，向外是正值，向里是负值

2. 3D位移translate3d

   3D移动在2D移动的基础上多加了一个可以移动的方向，就是z轴方向。

   - transform: translateX(100px);仅仅是在x轴上移动
   - transform: translateY(100px);仅仅是在Y轴上移动
   - transform: translateZ(100px);仅仅是在Z轴上移动（注意：translateZ一般用px单位）
   - transform: translate3d(x, y, z);其中x、y、z分别指要移动的轴的方向的距离

3. 透视perspective

   在2D平面产生近大远小视觉立体，但是只是效果二维的

   - 如果想要在网页产生3D效果需要透视（理解成3D物体投影在2D平面内）。
   - 模拟人类的视觉位置，可认为安排一只眼睛去看
   - 透视我们也称为视距：视距就是人的眼睛到屏幕的距离距离视觉点越近的在电脑平面成像越大，越远成像越小
   - 透视的单位是像素

   - **透视写在被观察元素的父盒子上面的**

4. 3D旋转rotate3d

   3D旋转指可以让元素在三维平面内沿着x轴，y轴，z轴或者自定义轴进行旋转。

   - transform: rotateX(45deg);沿着x轴正方向旋转45度

   - transform: rotateY(45deg);沿着y轴正方向旋转45度

   - transform: rotateZ(45deg);沿着z轴正方向旋转45度

   - transform: rotate3d(x, y, z, deg);沿着自定义轴旋转deg为角度（了解即可）

     transform: rotate3d(1, 0, 0, 45deg);就是沿着x轴旋转45deg

     transform: rotate3d(1, 1, 0, 45deg);就是沿着对角线旋转45deg

5. 3D呈现transform-style

   - 控制子元素是否开启三维立体环境。
   - transform-style: flat;子元素不开启3d立体空间（默认）
   - transform-style: preserve-3d;子元素开启立体空间
   - 代码写给父级，但是影响的是子盒子

   
