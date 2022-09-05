# 第一章 Vue基础

## 一，简介

是一套用于构建用户界面的**渐进式框架**。与其它大型框架不同的是，Vue 被设计为可以自底向上逐层应用。Vue 的核心库只关注视图层，不仅易于上手，还便于与第三方库或既有项目整合。另一方面，当与现代化的工具链以及各种支持类库结合使用时，Vue 也完全能够为复杂的单页应用提供驱动。

## 二，第一个Vue程序

```html
<body>
    <div id="app">
        {{ message }}
    </div>
    
    <!-- 开发环境版本，包含了有帮助的命令行警告 -->
    <script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
    <script>
        var app = new Vue({
            el: '#app',
            data: {
                message: 'Hello Vue!'
            }
        })
    </script>
</body>
```

1. el：挂载点
   - el是用来设置Vue实例挂载（管理）的元素
   - Vue会管理el选项命中的元素及其内部的后代元素
   - 可以使用其他的选择器，但是建议使用id选择器
   - 可以使用其他的双标签，不能使用HTML和BODY
2. data：数据对象
   - Vue中用到的数据定义在data中
   - data中可以写复杂类型的数据
   - 渲染复杂类型数据时，遵守js的语法即可

# 第二章 本地应用

## 一，Vue指令

1. v-text：设置标签的文本值（textContent）

   - v-text指令的作用是：设置标签的内容(textContent)
   - 默认写法会替换全部内容，使用差值表达式{{}}可以替换指定
   - 内容内部支持写表达式

   ```html
   <body>
       <div id="app">
           <h1 v-text="message"></h1>
           <!-- 插值写法 -->
           <h1>{{ message }},world</h1>
           <!-- 运算符 -->
           <h1 v-text="message + '!'"></h1>
       </div>
       <!-- 开发环境版本，包含了有帮助的命令行警告 -->
       <script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
       <script>
           var app = new Vue({
               el: "#app",
               data: {
                   message: "hello"
               }
           })
       </script>
   </body>
   ```

2. v-html：设置标签的innerHTML

   - v-html指令的作用是：设置元素的JinnerHTME
   - 内容中有html结构会被解析为标签
   - v-text指令无论内容是什么，只会解析为文本
   - 解析文本使用v-text，需要解析html结构使用v-html

   ```html
   <body>
       <div id="app">
           <p v-text="content"></p>
           <p v-html="content"></p>
       </div>
       <!-- 开发环境版本，包含了有帮助的命令行警告 -->
       <script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
       <script>
           var app = new Vue({
               el: "#app",
               data: {
                   content:"<a href='http://www.baidu.com'>百度</a>"
               }
           })
       </script>
   </body>
   ```

3. v-on：为元素绑定事件

   - v-on指令的作用是：为元素绑定事件
   - 事件名不需要写on
   - 指令可以简写为@
   - 绑定的方法定义在methods属性中
   - 方法内部通过this关键字可以访问定义在data中数据

   ```html
   <body>
       <div id="app">
           <input type="button" value="v-on指令" v-on:click="doIt">
           <!-- 简写 -->
           <input type="button" value="v-on指令" @click="doIt">
           <!-- 双击事件 -->
           <input type="button" value="v-on指令" @dblclick="doIt">
           <h2 @click="changeContent">{{ content }}</h2>
       </div>
       <!-- 开发环境版本，包含了有帮助的命令行警告 -->
       <script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
       <script>
           var app = new Vue({
               el: "#app",
               data: {
                   content: "吴功勋"
               },
               methods: {
                   doIt:function() {
                       alert("Do It!");
                   },
                   changeContent:function() {
                       this.content += "吴功勋";
                   }
               }
           })
       </script>
   </body>
   ```

4. v-show：根据表达式的真假，切换元素的显示和隐藏

   - v-show指令的作用是：根据真假切换元素的显示状态
   - 原理是修改元素的display,实现显示隐藏
   - 指令后面的内容，最终都会解析为布尔值
   - 值为true元素显示，值为false元素隐藏数据
   - 改变之后，对应元素的显示状态会同步更新

   ```html
   <body>
       <div id="app">
           <input type="button" value="切换显示状态" @click="changeIsShow">
           <img src="./111.jpg" v-show="isShow">
       </div>
       <!-- 开发环境版本，包含了有帮助的命令行警告 -->
       <script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
       <script>
           var app = new Vue({
               el: "#app",
               data: {
                   isShow:false
               },
               methods: {
                   changeIsShow:function() {
                       this.isShow = !this.isShow;
                   }
               }
           })
       </script>
   </body>
   ```

5. v-if：根据表达式的真假，切换元素的显示和隐藏

   - v-if指令的作用是：根据表达式的真假切换元素的显示状态
   - 本质是通过操纵dom元素来切换显示状态
   - 表达式的值为true,元素存在于dom树中，为false,从dom树中移除
   - 频繁的切换v-show,反之使用v-if,前者的切换消耗小

   ```html
   <body>
       <div id="app">
           <input type="button" value="切换显示" @click="toggleIsShow">
           <p v-if="isShow">吴功勋</p>
       </div>
       <!-- 开发环境版本，包含了有帮助的命令行警告 -->
       <script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
       <script>
           var app = new Vue({
               el: "#app",
               data: {
                   isShow:false
               },
               methods: {
                   toggleIsShow:function() {
                       this.isShow = !this.isShow;
                   },
               }
           })
       </script>
   </body>
   ```

6. v-bind：设置元素的属性

   - v-bind指令的作用是：为元素绑定属性
   - 完整写法是v-bind:属性名
   - 简写的话可以直接省略v-bind,只保留:属性名
   - 需要动态的增删class建议使用对象的方式

   ```html
   <body>
       <div id="app">
           <img v-bind:src="imgSrc">
           <!-- 简写方式 -->
           <img :src="imgSrc" :class="{active:isActive}" @click="toggleIsActive">
       </div>
       <!-- 开发环境版本，包含了有帮助的命令行警告 -->
       <script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
       <script>
           var app = new Vue({
               el: "#app",
               data: {
                   imgSrc:"./111.jpg",
                   imgTitle:"wgx",
                   isActive:false
               },
               methods: {
                   toggleIsActive:function() {
                       this.isActive = !this.isActive;
                   }
               }
           })
       </script>
   </body>
   ```

7. v-for：根据数据生成列表结构

   - v-for指令的作用是：根据数据生成列表结构
   - 数组经常和v-for结合使用语法是(item,index) in 数据
   - item和index可以结合其他指令一起使用
   - 数组长度的更新会同步到顶面上，是响应式的

   ```html
   <body>
       <div id="app">
           <ul>
               <li v-for="(item, index) in arr">
                   {{ index + 1 }}{{ item }}
               </li>
           </ul>
       </div>
       <!-- 开发环境版本，包含了有帮助的命令行警告 -->
       <script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
       <script>
           var app = new Vue({
               el: "#app",
               data: {
                   arr:["北京", "上海", "广州", "深圳"]
               }
           })
       </script>
   </body>
   ```

8. v-on补充：传递自定义参数，事件修饰符

   - 事件绑定的方法写成函数调用的形式，可以传入自定义参数
   - 定义方法时需要定义形参来接收传入的实参
   - 事件的后面跟上**.修饰符**可以对事件进行限制
   - .enter可以限制触发的按键为回车
   - 事件修饰符有多种

   ```html
   <body>
       <div id="app">
           <input type="button" value="点击" @click="doIt(666)">
           <!-- 点击enter键，弹出你好 -->
           <input type="text" @keyup.enter="sayHi">
       </div>
       <!-- 开发环境版本，包含了有帮助的命令行警告 -->
       <script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
       <script>
           var app = new Vue({
               el: "#app",
               methods: {
                   doIt:function(var1) {
                       console.log(var1);
                   },
                   sayHi:function() {
                       alert("你好!");
                   }
               }
           })
       </script>
   </body>
   ```

9. v-model：获取和设置表单元素的值（双向数据绑定）

   - v-model指令的作用是便捷的设置和获取表单元素的值
   - 绑定的数据会和表单元素值相关联
   - 绑定的数据←→表单元素的值

   ```html
   <body>
       <div id="app">
           <h2>{{ message }}</h2>
           <input type="text" v-model="message" @keyup.enter="alertMessage">
       </div>
       <!-- 开发环境版本，包含了有帮助的命令行警告 -->
       <script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
       <script>
           var app = new Vue({
               el: "#app",
               data: {
                   message:"吴功勋"
               },
               methods: {
                   alertMessage:function() {
                       alert(this.message);
                   }
               },
           })
       </script>
   </body>
   ```

# 第三章 网络应用

## 一，axios

1. 简介

   功能强大的网络请求库

2. 导包

   ```html
   <script src="https://unpkg.com/axios/dist/axios.min.js"></script>
   ```

   axios.get(地址?key=value&key2=value2).then(function(response){},function(err){})

   axios.post(地址,{key:value&key2:value2}).then(function(response){},function(err){})

   ```html
   <body>
       <input type="button" value="get" class="get">
       <input type="button" value="post" class="post">
       <script src="https://unpkg.com/axios/dist/axios.min.js"></script>
       <script>
           document.querySelector(".get").onclick = function() {
               axios.get("https://autumnfish.cn/api/joke/list?num=1")
               .then(function(response) {
                   console.log(response);
               },function(err) {
                   console.log(err);
               })
           }
           document.querySelector(".post").onclick = function() {
               axios.post("https://autumnfish.cn/api/user/reg",{username:"wgx"})
               .then(function(response) {
                   console.log(response);
               },function(err) {
                   console.log(err);
               })
           }
       </script>
   </body>
   ```

3. axios+vue

   - axios回调函数中的this已经改变，无法访问到data中数据
   - 把this保存起来，回调函数中直接使用保存的this即可
   - 和本地应用的最大区别就是改变了数据来源

   ```html
   <body>
       <div id="app">
           <input type="button" value="获取笑话" @click="getJoke">
           <p>{{ joke }}</p>
       </div>
       <script src="https://unpkg.com/axios/dist/axios.min.js"></script>
       <!-- 开发环境版本，包含了有帮助的命令行警告 -->
       <script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
       <script>
           var app = new Vue({
               el: "#app",
               data: {
                   joke:""
               },
               methods: {
                   getJoke:function() {
                       var that = this;
                       axios.get("https://autumnfish.cn/api/joke/list?num=1")
                       .then(function(response) {
                           that.joke = response.data.jokes[0];
                       }, function(err) {
                           that.joke = "出错误了";
                       })   
                   }
               }
           })
       </script>
   </body>
   ```