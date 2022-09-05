# 第一章 Vue核心

## 一，MVVM模型

1. M（模型，Model）：对应data中的数据

   V（视图，View）：模板

   VM（视图模型，ViewModel）：Vue实例对象

## 二，数据代理

1. Object.defineproperty

   ```javascript
   let person = {
       name: "wgx",
       gender: "男"
   }
   
   //定义属性age
   Object.defineProperty(person, "age", {
       value: 22,
       enumerable: true,//控制属性是否可以被枚举，默认是false
       writable: true,//控制属性是否可以被修改，默认是false
       configurable: true//控制属性是否可以被删除，默认是false
       // 当有人读取person的age属性的时候，get函数就会被调用，且返回值就是age的值
       // get(){
       //     return number;
       // }
   });
   
   console.log(person);
   ```

2. 数据代理

   ```javascript
   let obj1 = {x: 100};
   let obj2 = {y: 200};
   
   Object.defineProperty(obj2, "x", {
       get() {
           return obj1.x;
       },
       set(value) {
           obj1.x = value;
       }
   });
   ```

3. Vue中的数据代理

   - 通过vm对象来代理data对象中的属性的操作
   - 更加方便的操作data中的数据
   - 通过Object.defineproperty()把data对象中的所有属性添加到vm上，为每一个添加到vm上的属性都指定一个getter/setter，在getter/setter内部去操作data中对应的属性

## 三，事件处理

1. 事件修饰符

   - prevent：阻止默认事件

   - stop：阻止事件冒泡

   - once：事件只会触发一次

   - capture：使用时间的捕获模式

   - self：只有event.target是当前操作的元素时才触发事件

   - passive：事件的默认行为立即执行，无需等待事件回调执行完毕

   - **修饰符可以连着写**

   - ```html
     <body>
         <div id="root">
             <!-- 阻止默认事件 -->
             <a href="https://www.baidu.com" @click.prevent="showInfo">百度</a>
             <!-- 阻止事件冒泡 -->
             <div style="background-color: blue;" @click="showInfo">
                 <button @click.stop="showInfo" style="margin: 10px;">显示信息</button>
             </div>
             <!-- 事件只触发一次 -->
             <button @click.once="showInfo" style="margin: 10px;">显示信息</button>
             <!-- 使用事件捕获模式 -->
             <div id="div1" style="background-color: blue;margin: 10px;" @click.capture="showMsg(1)">
                 div1
                 <div id="div2" style="background-color: brown;margin: 10px;" @click="showMsg(2)">
                     div2
                 </div>
             </div>
             <!-- 只有event.target是当前操作的元素时才触发事件 -->
             <div style="background-color: blue;" @click.self="showInfo">
                 <button @click="showInfo" style="margin: 10px;">显示信息</button>
             </div>
             <!-- 事件的默认行为立即执行，无需等待事件回调执行完毕 -->
             <ul @wheel.passive="wheel" style="height: 300px;background-color: chocolate;overflow: auto;">
                 <li style="height: 100px;">1</li>
                 <li style="height: 100px;">2</li>
                 <li style="height: 100px;">3</li>
                 <li style="height: 100px;">4</li>
             </ul>
         </div>
         <script>
             new Vue({
                 el: "#root",
                 methods: {
                     showInfo() {
                         alert("hello");
                     },
                     showMsg(msg) {
                         alert(msg);
                     },
                     wheel() {
                         for (let i = 0; i < 100000; i++) {
                             console.log("1");
                         }
                     }
                 }
             });
         </script>
     </body>
     ```

2. 键盘事件

   - Vue中的常用按键别名
     - 回车：enter
     - 删除：delete（"删除键"和"退格键"）
     - 退出：esc
     - 空格：space
     - 换行：tab（特殊，必须配合keydown使用）
     - 上：up
     - 下：down
     - 左：left
     - 右：right
   - Vue未提供别名的按键，可以使用按键原始的key值去绑定，但注意要转为kebab-case（短横线命名）
   - 系统修饰键（用法特殊）：ctrl、alt、shift、meta
     - 配合keyup使用：按下修饰键同时，再按下其他键，随后释放其他键，事件才被触发
     - 配合keydown使用：正常触发事件
     - **系统修饰键可以连续写**
   - 也可以使用keyCode去指定具体按键（不推荐）
   - Vue.config.keyCode.自定义键名 = 键码，可以自定义按键别名

## 四，计算属性

1. 定义：要用的属性不存在，要通过已有属性计算得来。
2. 原理：底层借助了Objcet.defineproperty方法提供的getter和setter。
3. get函数什么时候执行？
   - 初次读取时会执行一次。
   - 当依赖的数据发生改变时会被再次调用。
4. 优势：与methods'实现相比，内部有级存机制（复用），效率更高，调试方便
5. 备注：
   - 计算属性最终会出现在vm上，直接读取使用即可。
   - 如果计算属性要被修改，那必须写set函数去响应修改，且set中要引起计算时依赖的数据发生改变

```html
<body>
    <div id="root">
        <input type="text" v-model="firstName"><br>
        <input type="text" v-model="lastName"><br>
        <span>{{ fullName }}</span>
    </div>
    <script>
        new Vue({
            el: "#root",
            data: {
                firstName: "wu",
                lastName: "gongxun"
            },
            computed: {
                fullName: {
                    get() {
                        return this.firstName + "-" + this.lastName;
                    }
                }
            }
        });
    </script>
</body>
```

**计算属性简写**

```javascript
const vm = new Vue({
    el: "#root",
    data: {
        firstName: "wu",
        lastName: "gongxun"
    },
    computed: {
        fullName() {
            return this.firstName + "-" + this.lastName;
        }
    }
});
```

## 五，监视属性

1. 当被监视的属性变化时，回调函数自动调用，进行相关的操作

2. 监视的属性必须存在，才能进行监视

3. 监视的两种写法

   - new Vue()时传入watch配置

     ```javascript
     let vm = new Vue({
         el: "#root",
         data: {
             isHot: true
         },
         methods: {
             changeWeather() {
                 this.isHot = !this.isHot;
             }
         },
         computed: {
             info() {
                 return this.isHot ? "炎热" : "凉爽";
             }
         },
         watch: {
             isHot:{
                 immediate: true,//初始化时让handler调用一下
                 //当isHot发生改变时，handler被调用
                 handler(newValue, oldValue) {
                     console.log(newValue, oldValue)
                 }
             }
         }
     });
     ```

   - 通过vm.$watch监视

     ```javascript
     let vm = new Vue({
         el: "#root",
         data: {
             isHot: true
         },
         methods: {
             changeWeather() {
                 this.isHot = !this.isHot;
             }
         },
         computed: {
             info() {
                 return this.isHot ? "炎热" : "凉爽";
             }
         },
         // watch: {
         //     isHot:{
         //         immediate: true,//初始化时让handler调用一下
         //         //当isHot发生改变时，handler被调用
         //         handler(newValue, oldValue) {
         //             console.log(newValue, oldValue)
         //         }
         //     }
         // }
     });
     vm.$watch("isHot", {
         immediate: true,
         handler(newValue, oldValue) {
             console.log(newValue, oldValue);
         }
     });
     ```

**深度监视**

1. Vue中的watch默认不检测对象内部值的改变（一层）
2. 配置deep:true可以检测对象内部值的改变（多层）

```javascript
numbers: {
    immediate: true,
    deep: true,//监视多级结构中的某个属性的变化
    handler(newValue, oldValue) {
        console.log(newValue, oldValue);
    }
}
```

**监视属性的简写**

```javascript
let vm = new Vue({
    el: "#root",
    data: {
        isHot: true
    },
    methods: {
        changeWeather() {
            this.isHot = !this.isHot;
        }
    },
    computed: {
        info() {
            return this.isHot ? "炎热" : "凉爽";
        }
    },
    // watch: {
    //     isHot(newValue, oldValue) {
    //         console.log(newValue, oldValue);
    //     }
    // }
});
vm.$watch("isHot", function (newValue, oldValue) {
    console.log(newValue, oldValue);
});
```

## 六，条件渲染

1. v-if
   写法：

   - v-if="表达式”
   - v-else-if="表达式"
   - v-else="表达式"

   适用于：切换频率较低的场景。
   特点：不展示的DOM元素直接被移除。
   注意：v-if可以和：v-else-if、v-else一起使用，但要求结构不能被“打断”。

2. v-show
   写法：v-show="表达式”
   适用于：切换频率较高的场景。
   特点：不展示的D0M元素未被移除，仅仅是使用样式隐藏掉

3. 备注：
   使用v-if的时，元素可能无法获取到，而使用v-show一定可以获取到。

## 七，列表渲染

1. v-for指令

   - 用于遍历展示数据
   - 语法：v-for="(item, index) in items" :key="index"
   - 可以遍历数组、对象、字符串、指定次数

2. key的作用与原理

   - 虚拟DOM中key的作用：
     key是虚拟DOM对象的标识，当状态中的数据发生变化时，Vue会根据【新数据】生成【新的虚拟DOM】，随后Vue进行【新虚拟DOM】与【旧虚拟DOM】的差异比较，比较规则如下：
   - 对比规则：
     - 旧虚拟DOM中找到了与新虚拟DOM相同的key:
       若虚拟DOM中内容没变，直接使用之前的真实DOM!
       若虚拟D0M中内容变了，则生成新的真实DOM,随后替换掉页面中之前的真实DOM。
     - 旧虚拟DOM中未找到与新虚拟DOM相同的key
       创建新的真实DOM,随后渲染到到页面。
   - 用index作为key可能会引发的问题：
     - 若对数据进行：逆序添加、逆序删除等破坏顺序操作：会产生没有必要的真实DOM更新==>界面效果没问题，但效率低。
     - 如果结构中还包含输入类的DOM:会产生错误DOM更新==>界面有问题。
   - 开发中如何选择key:
     最好使用每条数据的唯一标识作为key，比如id、手机号、身份证号、学号等唯一值。如果不存在对数据的逆序添加、逆序删除等破坏顺序操作，仅用于渲染列表用于展示，使用index作为key是没有问题的。

3. 列表过滤

   ```html
   <div id="root">
      <h2>人员列表</h2>
      <input type="text" placeholder="请输入名字" v-model="keyWord">
      <ul>
         <li v-for="(person, index) in filterPersons" :key="person.id">
            {{person.name}} - {{person.age}} - {{person.gender}}
         </li>
      </ul>
   </div>
   <script>
      new Vue({
         el: "#root",
         data: {
            keyWord: "",
            persons: [
               {id:"001", name:"马冬梅", age:18, gender:"女"},
               {id:"002", name:"周冬雨", age:19, gender:"女"},
               {id:"003", name:"周杰伦", age:20, gender:"男"}
            ]
         },
         computed: {
            filterPersons() {
               return this.persons.filter((person) => {
                  return person.name.indexOf(this.keyWord) != -1;
               });
            }
         }
      });
   </script>
   ```

4. 列表排序

   ```html
   <div id="root">
      <h2>人员列表</h2>
      <input type="text" placeholder="请输入名字" v-model="keyWord">
      <button @click="sortType = 2">年龄升序</button>
      <button @click="sortType = 1">年龄降序</button>
      <button @click="sortType = 0">原顺序</button>
      <ul>
         <li v-for="(person, index) in filterPersons" :key="person.id">
            {{person.name}} - {{person.age}} - {{person.gender}}
         </li>
      </ul>
   </div>
   <script>
      new Vue({
         el: "#root",
         data: {
            keyWord: "",
            persons: [
               {id:"002", name:"周冬雨", age:19, gender:"女"},
               {id:"001", name:"马冬梅", age:18, gender:"女"},
               {id:"003", name:"周杰伦", age:20, gender:"男"}
            ],
            sortType: 0//0原顺序，1降序，2升序
         },
         computed: {
            filterPersons() {
               const arr = this.persons.filter((person) => {
                  return person.name.indexOf(this.keyWord) != -1;
               });
               if (this.sortType) {
                  //排序
                  arr.sort((p1, p2) => {
                     return this.sortType == 1 ? p2.age - p1.age : p1.age - p2.age;
                     });
               }
               return arr;
            }
         },
         methods: {
         
         }
      });
   </script>
   ```

## 八，Vue的数据监测原理

1. vue会监视data中所有层次的数据

2. 如何监测对象中的数据？

   通过setter实现监视，且要在new Vue时就传入要监测的数据。

   - 对象中后追加的属性，Vue默认不做响应式处理

   - 如需给后添加的属性做响应式，请使用下API：

     Vue.set(target, propertyName/index, value)

     vm.$set(target,propertyName/index,value)

3. 如何监测数组中的数据？

   通过包裹数组更新元素的方法实现，本质就是做了两件事：

   - 调用原生对应的方法对数组进行更新。
   - 重新解析模板，进而更新页面。

4. 在Vue修改数组中的某个元素一定要用到如下方法

   push()、pop()、shift()、unshift()、splice()、sort()、reverse()

   **注意：Vue.set()和vm.$set()不能给vm或者vm的根数据添加属性**

5. 

6. ```html
   <div id="root">
      <h1>学生信息</h1>
      <hr>
      <button @click="student.age++">年龄加一岁</button><br>
      <button @click="addGender">添加性别属性，默认值：男</button><br>
      <button @click="student.friends.unshift({name: 'tom', age: 32})">在列表首位添加一个朋友</button><br>
      <button @click="student.friends[0].name = '张三'">修改第一个朋友的名字为：张三</button><br>
      <button @click="student.hobby.push('javascript')">添加一个爱好</button><br>
      <button @click="student.hobby.splice(0, 1, 'spring')">修改第一个爱好为：spring</button><br>
      <h2>姓名:{{student.name}}</h2>
      <h2>年龄:{{student.age}}</h2>
      <h2 v-if="student.gender != undefined">性别:{{student.gender}}</h2>
      <h2>爱好:</h2>
      <ul>
         <li v-for="(h, index) in student.hobby">
            {{h}}
         </li>
      </ul>
      <h2>朋友:</h2>
      <ul>
         <li v-for="(f, index) in student.friends">
            {{f.name}}--{{f.age}}
         </li>
      </ul>
   </div>
   <script>
      const vm = new Vue({
         el: "#root",
         data: {
            student: {
               name: "wgx",
               age: 22,
               hobby: ["java", "C++", "Vue"],
               friends: [{
                  name: "jack",
                  age: "23"
               }, {
                  name: "marry",
                  age: 18
               }]
            }
         },
         methods: {
            addGender() {
               Vue.set(this.student, 'gender', '男');
            }
         }
      });
   </script>
   ```

## 九，收集表单数据

1. \<input type="text"/>，则v-model收集的是value值，用户输入的就是value值

2. \<input type="radio"/>，则v-model收集的是value值，且要给标签配置value值

3. \<input type="checkbox"/>

   - 没有配置input的value属性，那么收集的就是checked(勾选or未勾选，是布尔值)

   - 配置input的value属性：

     v-model的初始值是非数组，那么收集的就是checked(勾选or未勾选，是布尔值

     v-model的初始值是数组，那么收集的的就是value组成的数组

4. v-model的三个修饰符：

   - lazy:失去焦点再收集数据
   - number:输入字符非转为有效的数字
   - trim:输入首尾空格过滤

## 十，过滤器

1. 定义

   对要显示的数据进行特定格式化后再显示（适用于一些简单逻辑的处理）

2. 语法

   - 注册过滤器：Vue.filter(name, callback)或new Vue(filters:{})
   - 使用过滤器：{{(xxx | 过滤器名)}}或v-bind:属性="xxx | 过滤器名"

3. 备注

   - 过滤器也可以接收额外参数、多个过滤器也可以串联
   - 并没有改变原本的数据，是产生新的对应的数据

4. 

5. ```html
   <body>
      <div id="root">
         <h1>当前时间</h1><br>
         <h1>{{time | timeFormater}}</h1>
         <h1>{{time | timeFormater("YYYY年MM月DD日")}}</h1>
      </div>
   <script>
      new Vue({
         el: "#root",
         data: {
            time: Date.now()
         },
         filters: {
            //形参的默认值
            timeFormater(val, str="YYYY-MM-DD HH:mm:ss") {
               return dayjs(val).format(str);
            }
         }
      });
   </script>
   ```

## 十一，额外的指令

1. v-cloak（没有值）
   - 本质是一个特殊属性，Vue实例创建完毕并接管容器之后，会删掉v-cloak属性
   - 使用css配置v-cloak可以解决网速慢时页面展示出{{XXX}}的问题
2. v-once（没有值）
   - v-once所在节点在初次动态渲染后，就视为静态内容了。
   - 以后数据的改变不会引起v-once所在结构的更新，可以用于优化性能。
3. v-pre（没有值）
   - 跳过其所在节点的编译过程。
   - 可利用它跳过没有使用指令语法、没有使用插值语法的节点，会加快编译。

## 十二，自定义指令

1. 函数式

   ```html
   <div id="root">
      <h1>当前的n值是:<span v-text="n"></span></h1>
      <!--定义一个v-big指令，会把绑定的数值放大十倍-->
      <h1>扩大十倍的n值是:<span v-big="n"></span></h1>
      <button @click="n++">点击n++</button>
   </div>
   <script>
      new Vue({
         el: "#root",
         data: {
            n: 1
         },
         directives: {
            //指令与元素成功绑定时调用
            //指令所在的模板重新解析时被调用
            big(element, binding) {
               element.innerText = binding.value * 10
            }
         }
      });
   </script>
   ```

2. 对象式

   ```html
   <div id="root">
      <h1>当前的n值是:<span v-text="n"></span></h1>
      <!--定义一个v-big指令，会把绑定的数值放大十倍-->
      <h1>扩大十倍的n值是:<span v-big="n"></span></h1>
      <button @click="n++">点击n++</button>
      <hr>
      <!--定义一个v-fbind指令，可以让绑定的input元素默认获取焦点-->
      <input type="text" v-fbind:value="n">
   </div>
   <script>
      new Vue({
         el: "#root",
         data: {
            n: 1
         },
         directives: {
            //指令与元素成功绑定时调用
            //指令所在的模板重新解析时被调用
            big(element, binding) {
               element.innerText = binding.value * 10
            },
            fbind: {
               //指令与元素成功绑定时
               bind(element, binding) {
                  element.value = binding.value;
               },
               //指令所在的元素被插入页面时
               inserted(element, binding) {
                  element.focus();
               },
               //指令所在的模板被重新解析时
               update(element, binding) {
                  element.value = binding.value;
                  element.focus();
               }
            }
         }
      });
   </script>
   ```

## 十三，Vue生命周期

1. beforeCreate

   此时无法通过vm访问到data中的数据、methods中的方法。

2. created

   此时可以通过vm访问到data中的欧据、methods中配置的方法

3. beforeMount

   - 页面呈现的是未经Vue编译的DOM结构.
   - 所有对DOM的操作，最终都不奏效

4. mounted

   - 页面中星现的是经过Vue编译的DOM。
   - 对DOM的操作均有效（尽可能避免）
   - 至此初始化过程结束，一般在此进行：开启定时器、发送网络请求、订阅消息、绑定自定义事件、等初始化操作。

5. beforeUpdate

   此时数据是新的，但页面是旧的，即：页面尚末和数据保持同步。

6. updated

   此时数据是新的，页面也是新的，即：页面和数据保持同步。

7. beforeDestroy

   此时vm中所有的：data、methods、指令等等，都处于可用状态，马上要执行销毁过程，一般在此阶段：关闭定时器、取消订阅消息，解绑自定义事件等收尾操作

8. destroyed

   销毁

9. ![](F:\截图\lifecycle.png)

# 第二章 Vue组件

## 一，模块与组件、模块化与组件化

1. 模块

   - 定义：向外提供特定功能的js程序，一般就是一个js文件和
   - 作用：复用js,简化js的偏写，提高js运行效率

2. 组件

   - 定义：用来实现局部（特定）功能效果的代码集合(html/css/js/image...)
   - 作用：复用编码，简化项目编码，提高运行效率

3. 模块化

   当应用中的js都以模块来编写的，那这个应用就是一个模块化的应用。

4. 组件化

   当应用中的功能都是多组件的方式来编写的，那这个应用就是一个组件化的应用。

## 二，非单文件组件

1. 基本使用

   ```html
   <div id="root">
      <!--3. 编写组件标签-->
      <school></school>
      <hr>
      <student></student>
   </div>
   <script>
      //1. 创建组件
      const school = Vue.extend({
         //必须写成函数式
         data() {
            return {
               name: "皖西学院",
               address: "六安"
            }
         },
         template: `
            <div>
               <h1>学校名称:{{name}}</h1>
               <h1>学校地址:{{address}}</h1>
            </div>
         `
      });
   
       // 简写形式
       // const school =  {
       // 	data() {
       // 		return {
       // 			name: "皖西学院",
       // 			address: "六安"
       // 		}
       // 	},
       // 	template: `
       // 		<div>
       // 			<h1>学校名称:{{name}}</h1>
       // 			<h1>学校地址:{{address}}</h1>
       // 		</div>
       // 	`
       // }
      
      const student = Vue.extend({
         data() {
            return {
               name: "wgx",
               age: 22
            }
         },
         template: `
            <div>
               <h1>学生姓名:{{name}}</h1>
               <h1>学生年龄:{{age}}</h1>
            </div>
         `
      });
       
      /*全局注册*/
      // Vue.component("school", school);
      
      new Vue({
         el: "#root",
         //2.注册组件（局部注册）
         components: {
            school,
            student
         }
      });
   </script>
   ```

2. 关于组件名

   - 一个单词组成：
     - 第一种写法（首字母小写）：school
     - 第二种写法（首字母大写）：School
   - 多个单词组成：
     - 第一种写法(kebab-case命名)：my-school
     - 第二种写法(CamelCase命名)：MySchool(需要Vue脚手架支持)
   - 注意
     - 组件名尽可能回避HTML中已有的元素名称，例：h2、H2都不行。
     - 可以使用name配置项指定组件在开发者工具中呈现的名字。

3. 关于标签

   - 第一种法：\<school></school\>
   - 第二种写法：\<school/>
   - 备注：不用使用脚手架时，<school/\>会导致后续组件不能宿染。

4. 组件的嵌套

   ```html
   <div id="root">
   
   </div>
   <script>
      const student = Vue.extend({
         data() {
            return {
               name: "wgx",
               age: 22
            }
         },
         template: `
            <div>
               <h1>学生姓名:{{name}}</h1>
               <h1>学生年龄:{{age}}</h1>
            </div>
         `
      });
      
      const school = Vue.extend({
         data() {
            return {
               name: "皖西学院",
               address: "六安"
            }
         },
         template: `
            <div>
               <h1>学校名称:{{name}}</h1>
               <h1>学校地址:{{address}}</h1>
               <student></student>
            </div>
         `,
         components: {
            student
         }
      });
      
      const app = Vue.extend({
         template: `
            <school></school>
         `,
         components: {
            school
         }
      });
      
      new Vue({
         el: "#root",
         template: `
            <app></app>
         `,
         components: {
            app
         }
      });
   </script>
   ```

## 三，关于VueComponent

1. school组件本质是一个名为VueComponent的构造函数，且不是程序员定义的，是Vue.extend生成的

2. 我们只需要写<school/\>或<\school>\</school>，Vue解析时会帮我们创建school组件的实例对象，即Vue帮我们执行的：new VueComponent(options)。

3. 特别注意：每次调用Vue.extend，返回的都是一个全新的VueComponent

4. 关于this指向

   - 组件配置中：

     data函数、methods中的函数、watch中的函数、computed中的函数它们的this均是VueComponent实例

   - new Vue()配置中：

     data函数、methods中的函数、watch中的函数、computed中的函数它们的this均是Vue实例

5. VueComponent的实例对象，以后简称vc(也可称之为：组件实例对象)。Vue的实例对象，以后简称vm。

**一个重要的内置关系**

1. VueComponent.prototype.\_\_proto\_\_ === Vue.prototype
2. 为什么要有这个关系：让组件实例对象(vc)可以访问到Vue原型上的属性、方法。
3. ![](F:\截图\屏幕截图 2022-08-28 155448.png)

## 四，单文件组件

- School.vue

  ```vue
  <template>
    <!--组件的结构-->
    <div class="demo">
      <h1>学校名称:{{name}}</h1>
      <h1>学校地址:{{address}}</h1>
    </div>
  </template>
  
  <script>
    //组件相关的代码（数据、方法等）
    export default {
      name: "School",
      data() {
        return {
          name: "皖西学院",
          address: "六安"
        }
      }
    }
  </script>
  
  <style>
    /*组件的样式*/
    .demo{
      background: orange;
    }
  </style>
  ```

- Student.vue

  ```vue
  <template>
    <div>
      <h1>学生姓名:{{name}}</h1>
      <h1>学生年龄:{{age}}</h1>
    </div>
  </template>
  
  <script>
   export default {
     name: "Student",
     data() {
       return {
         name: "wgx",
         age: 22
       }
     }
   }
  </script>
  ```

- App.vue

  ```vue
  <template>
      <div>
          <school></school>
          <student></student>
      </div>
  </template>
  
  <script>
  import School from "./School";
  import Student from "./Student";
  
  export default {
      name: "App",
      components: {
          School,
          Student
      }
  }
  </script>
  ```

- main.js

  ```javascript
  import App from "./App";
  
  new Vue({
      el: "#root",
      template: `<App></App>`,
      components: {
          App
      }
  });
  ```

- index.html

  ```html
  <body>
     <div id="root"></div>
     <script src="vue.js"></script>
     <script src="main.js"></script>
  </body>
  ```

# 第三章 Vue脚手架

## 一，初始化脚手架

1. 具体步骤

   - 全局安装@vue/cli

     npm install -g @vue/cli

   - 切换到你要创建项目的目录，然后使用命令创建项目

     vue create xxxx

   - 启动项目

     npm run serve

   **注意**

   1. 如果出现下载缓慢，配置npm淘宝镜像：npm config set registry https://registry .npm.taobao.org
   2. vue脚手架隐藏了所有webpack相关的配置，若想查看具体的webpack配置，执行：vue inspect > output.js

2. 脚手架文件结构

   > node_modules：依赖
   >
   > public
   >
   > > favicon.ico：页签图标
   >
   > src
   >
   > > assets：存放静态资源
   > >
   > > > logo.png
   > >
   > > components：存放组件
   > >
   > > > HelloWorld.vue
   > >
   > > APP.vue：汇总所有组件
   > >
   > > mian.js：入口文件
   >
   > .gitignore：git版本控制忽略配置
   >
   > babel.config.js：babel的配置文件
   >
   > package.json：应用包配置文件
   >
   > README.md：应用描述文件
   >
   > package-lock.json：包版本控制文件
   >
   > vue.config.js：脚手架的配置文件

3. ref属性

   - 被用来给元素或子组件注册引用信息(id的替代者)

   - 应用在html标签上获取的是真实DOM元素，应用在组件标签上是组件实例对象(VC)

   - 使用方式：

     打标识：<h1 ref="xxx"\>....</h1>或<School ref="xxx"\>\</School>

     获取：this.$refs.xxx

4. props配置

   让组件接收外部传过来的数据

   - 传递数据：<Demo name="xxx"/\>

   - 接收数据

     - 简单接收：props: ['name']

     - 限制类型：props: {name: Number}

     - 限制类型，限制必要性，指定默认值：

       ```vue
       props: {
           name: {
               type: String,
               required: true,
               default: "wgx"
           }
       }
       ```

5. mixin

   - 创建mixin.js

     ```js
     export const mixin = {
         methods: {
             showName() {
                 alert(this.name);
             }
         }
     }
     ```

   - 在组件中使用

     ```vue
     <template>
         <div>
             <h1 @click="showName">学生姓名:{{name}}</h1>
             <h1>学生年龄:{{age}}</h1>
             <h1>学生性别:{{gender}}</h1>
         </div>
     </template>
     
     <script>
     import {mixin} from "../mixin"
     export default {
         name: "Student",
         data() {
             return {
                 name: "wgx",
                 age: 22,
                 gender: "男"
             }
         },
         mixins: [mixin]
     }
     </script>
     ```

6. 插件

   用于增强Vue，本质是包含install方法的一个对象，install的第一个参数时Vue，第二个以后的参数时插件使用者传递的数据

   - 定义插件

     对象.install = function(Vue, options) {

     ​		//添加全局过滤器

     ​		Vue.filter(...)

     ​		//添加全局指令

     ​		Vue.directive(...)

     ​		//配置全局混入

     ​		Vue.mixin(...)

     ​		//添加实例方法

     ​		Vue.prototype.$myMethod = function() {...}

     ​		Vue.prototype.$myProperty = ...

     }

   - 使用插件

     Vue.use(...)

## 二，TodoList案例

1. 组件化编码流程
   - 实现静态组件：抽取组件，使用组件实现静态页面效果
   - 展示动态数据：
     - 数据的类型、名称是什么
     - 数据保存在哪个组件
   - 交互——从绑定事件监听开始
2. props适用于
   - 父组件==>子组件 通信
   - 子组件==>父组件 通信（要求父先给子一个函数）
3. 使用v-model时要切记：v-model绑定的值不能是props传过来的值，因为props是不可以修改的
4. props传过来的若是对象类型的值，修改对象中的属性时Vue不会报错，但是不推荐这样做

## 三，自定义事件

1. 绑定

   - 父组件

     ```vue
     <template>
         <div>
             <School ref="school" @showName="showName"></School>
             <hr>
             <Student name="wgx" gender="男" @showName="showName"></Student>
         </div>
     </template>
     
     <script>
     import School from "./components/School";
     import Student from "./components/Student";
     export default {
         name: "APP",
         components: {
             School,
             Student
         },
         methods: {
             showName(name) {
                 console.log(name);
             }
         }
     }
     </script>
     ```

   - 子组件

     ```vue
     <template>
         <div>
             <h1 @click="showName">{{name}}</h1>
             <h1>{{address}}</h1>
         </div>
     </template>
     
     <script>
     export default {
         name: "School",
         data() {
             return {
                 name: "皖西学院",
                 address: "六安"
             }
         },
         methods: {
             showName() {
                 this.$emit("showName", this.name);
             }
         }
     }
     </script>
     ```

2. 解绑

   ```js
   unbind() {
       this.$off("showName");//解绑一个自定义事件
       this.$off(["showName"]);//解绑多个自定义事件
       this.$off();//解绑所有自定义事件
   }
   ```

3. 总结

   - 一种组件间通信的方式，适用于：子组件===>父组件

   - 使用场景：A是父组件，B是子组件，B想给A传数据，那么就要在A中给B绑定自定义事件（事件的回调在A中）。

   - 绑定自定义事件：

     - 第一种方式，在父组件中：<Demo @eventDemo="test"/>或<Demo v-on:eventDemo="test"/\>

     - 第二种方式，在父组件中：

       ```js
       <Demo ref="demo"/>
       ...
       mounted(){
           this.$refs.demo.$on('eventDemo',this.test)
       }
       ```

     - 若想让自定义事件只能触发一次，可以使用once修饰符，或$once方法

   - 触发自定义事件：this.$emit('eventDemo',数据)

   - 解绑自定义事件：this.$off('eventDemo')

   - 组件上也可以绑定原生DOM事件，需要使用native修饰符。

   - 注意：通过this.$refs.xxx.$on('eventDemo',回调)绑定自定义事件时，回调要么配置在methods中，要么用箭头函数，否则this指向会出问题！

## 四，全局事件总线

1. 一种组件间通信的方式，适用于任意组件间通信

2. 安装全局事件总线

   ```js
   new Vue({
     el: "#app",
     render: h => h(App),
     beforeCreate() {
       Vue.prototype.$bus = this//创建全局事件总线
     }
   });
   ```

3. 使用事件总线

   ```js
   mounted() {
       this.$bus.$on("receiveName", (data) => {
           console.log(data);
       });
   }
   
   
   methods: {
       sendName() {
           this.$bus.$emit("receiveName", this.name);
       }
   }
   ```

4. 最好在beforeDestroy解绑当前组件用到的事件总线

## 五，消息订阅与发布（pubsub）

1. 一种组件间通信的方式，适用于任意组件间通信

2. 使用步骤

   - 安装pubsub：npm i pubsub-js

   - 引入：import pubsub from "pubsub-js"

   - 接受数据

     ```js
     mounted() {
         this.pubId = pubsub.subscribe("receiveName", (msgName, data) => {
             console.log(data);
         });
     },
     beforeDestroy() {
         pubsub.unsubscribe(this.pubId);
     }
     ```

   - 发送数据

     ```js
     methods: {
         sendName() {
             pubsub.publish("receiveName", this.name)
         }
     }
     ```

   - 最好是在beforeDestroy中，用pubsub.unsubscribe(pubId);去取消订阅

**nextTick**

- 语法：this.$nextTick(回调函数)
- 作用：在下一次DOM更新结束后执行其指定的回调
- 什么时候用：当改变数据后，要基于更新后的新DOM进行某些操作时，要在nextTick所指定的回调函数中执行

## 六，Vue脚手架配置代理

1. 在vue.config.js中添加配置

   ```js
   devServer: {
       proxy: "http://localhost:8000"
   }
   ```

   - 优点：配置简单，请求资源时直接发给前端(8080)即可。
   - 缺点：不能配置多个代理，不能灵活的控制请求是否走代理。
   - 工作方式：若按照上述配置代理，当请求了前端不存在的资源时，那么该请求会转发给服务器（优先匹配前端资源）

2. 编写vue.config.js配置具体代理规则

   ```js
   devServer: {
     proxy: {
       "/api": {//匹配所有以"/api"开头的请求路径
         target: "http://localhost:8000",//代理目标的基础路径
         pathRewrite: {
           "^/api": ""
         },
         ws: true,
         changeOrigin: true
       }
     }
   }
   ```

   - 优点：可以配置多个代理，且可以灵活的控制请求是否走代理。
   - 缺点：配置略微繁琐，请求资源时必须加前缀。

## 七，插槽

1. 作用：让父组件可以向子组件指定位置插入HTML结构，也是一种组件间通信的方式，适用于父组件===>子组件

2. 分类：默认插槽，具名插槽，作用域插槽

3. 使用方式

   - 默认插槽

     ```html
     //父组件中
     <Category>
     	<div>html结构</div>	
     </Category>
     //子组件中
     <template>
     	<div>
             <slot>插槽默认内容</slot>
         </div>
     </template>
     ```

   - 具名插槽

     ```html
     //父组件中
     <Category>
     	<template slot="center">
         	<div>html结构</div>
         </template>
         <template v-slot:footer>
         	<div>html结构</div>
         </template>
     </Category>
     
     //子组件中
     <template>
     	<div>
             <slot name="center"></slot>
             <slot name="footer"></slot>
         </div>
     </template>
     ```

   - 作用域插槽

     数据在组件的自身，但根据数据生成的结构需要组件的使用者来决定，(games数据在Category组件中，但使用数据所遍历出来的结构由App组件决定)

     ```html
     //父组件中
     <Category>
     	<template scope="scopeData">
             <!--生成ul列表-->
             <ul>
                 <li v-for="game in scopeData.games" :key="game">{{game}}</li>
             </ul>
         </template>
         	<template scope="scopeData">
             <!--生成h4标题-->
     		<h4 v-for="game in scopeData.games" :key="game">{{game}}</h4>
         </template>
     </Category>
     
     //子组件中
     <template>
     	<div>
             <slot :games="games"></slot>
         </div>
     </template>
     <script>
     export default {
         name: "Category",
         //数据在子组件本身
         data() {
             return {
                 games: ["红色警戒", "英雄联盟", "超级玛丽"]
             }
         }
     }
     </script>
     ```

# 第四章 Vuex

## 一，Vuex是什么

1. 概念：专门在Vue中实现集中式状态（数据）管理的一个Vue插件，对Vue应用中多个组件的共享状态进行集中式管理（读/写），也是一种组件间的通信的方式，且适用于任意组件间通信

2. 什么时候使用Vuex

   - 多个组件依赖于同一状态
   - 来自不同的组件的行为需要变更同一状态

3. Vuex的工作原理

   ![](F:\截图\vuex.png)

## 二，搭建Vuex

1. 安装：npm i vuex@3（vue2中必须使用vuex3）

2. 使用：Vue.use(Vuex);

3. 创建文件：src/store/index.js

   ```js
   //该文件用于创建Vuex中最为核心的store
   
   //引入Vuex
   import Vuex from "vuex";
   //引入Vue
   import Vue from "vue";
   
   //使用Vuex
   Vue.use(Vuex);
   
   //准备actions-用于响应组件中的动作
   const actions = {};
   
   //准备mutations-用于操作数据(state)
   const mutations = {};
   
   //准备state-用于存储数据
   const state = {};
   
   //创建并暴露store
   export default new Vuex.Store({
       actions, mutations, state
   });
   ```

4. 在main.js中引入store

   ```js
   import Vue from 'vue'
   import App from './App.vue'
   import store from "@/store";
   
   Vue.config.productionTip = false;
   
   new Vue({
     el: "#app",
     render: h => h(App),
     store
   });
   ```

5. 使用

   ```vue
   <template>
       <div>
           <h1>当前的值为:{{$store.state.sum}}</h1>
           <select v-model.number="n">
               <option value="1">1</option>
               <option value="2">2</option>
               <option value="3">3</option>
           </select>
           <button @click="increment">+</button>
           <button @click="decrement">-</button>
           <button @click="incrementOdd">当前为奇数再加</button>
           <button @click="incrementWait">等一等再加</button>
       </div>
   </template>
   
   <script>
   export default {
       name: "Count",
       data() {
           return {
               n: 1
           }
       },
       methods: {
           increment() {
               this.$store.commit("INCREMENT", this.n);
           },
           decrement() {
               this.$store.commit("DECREMENT", this.n);
           },
           incrementOdd() {
               this.$store.dispatch("incrementOdd", this.n)
           },
           incrementWait() {
               this.$store.dispatch("incrementWait", this.n);
           }
       }
   }
   </script>
   
   <style scoped>
       button {
           margin-left: 5px;
       }
   </style>
   ```

6. index.js中的相应方法

   ```js
   //准备actions-用于响应组件中的动作
   const actions = {
       // increment(context, n) {
       //     context.commit("INCREMENT", n);
       // },
       // decrement(context, n) {
       //     context.commit("DECREMENT", n);
       // },
       incrementOdd(context, n) {
           if (context.state.sum % 2 != 0) {
               context.commit("INCREMENT", n);
           }
       },
       incrementWait(context, n) {
           setTimeout(() => {
               context.commit("INCREMENT", n);
           }, 500);
       }
   };
   
   //准备mutations-用于操作数据(state)
   const mutations = {
       INCREMENT(state, n) {
           state.sum += n;
       },
       DECREMENT(state, n) {
           state.sum -= n;
       }
   };
   ```

7. 借助mapState生成计算属性

   ```js
   import {mapState} from "vuex";
   export default {
       name: "Count",
       data() {
           return {
               n: 1
           }
       },
       computed: {
           // sum() {
           //     return this.$store.state.sum;
           // },
           //等价于上面
           ...mapState(["sum"])
       },
       methods: {
           increment() {
               this.$store.commit("INCREMENT", this.n);
           },
           decrement() {
               this.$store.commit("DECREMENT", this.n);
           },
           incrementOdd() {
               this.$store.dispatch("incrementOdd", this.n)
           },
           incrementWait() {
               this.$store.dispatch("incrementWait", this.n);
           }
       }
   }
   ```

8. 借助mapMutations, mapActions生成方法

   ```vue
   <template>
       <div>
           <h1>当前的值为:{{sum}}</h1>
           <select v-model.number="n">
               <option value="1">1</option>
               <option value="2">2</option>
               <option value="3">3</option>
           </select>
           <button @click="increment(n)">+</button>
           <button @click="decrement(n)">-</button>
           <button @click="incrementOdd(n)">当前为奇数再加</button>
           <button @click="incrementWait(n)">等一等再加</button>
       </div>
   </template>
   
   <script>
   import {mapState, mapMutations, mapActions} from "vuex";
   export default {
       name: "Count",
       data() {
           return {
               n: 1
           }
       },
       computed: {
           // sum() {
           //     return this.$store.state.sum;
           // },
           //等价于上面
           ...mapState(["sum"])
       },
       methods: {
           // increment() {
           //     this.$store.commit("INCREMENT", this.n);
           // },
           // decrement() {
           //     this.$store.commit("DECREMENT", this.n);
           // },
           //借助mapMutations生成对应方法
           ...mapMutations({increment: "INCREMENT", decrement: "DECREMENT"}),
           // incrementOdd() {
           //     this.$store.dispatch("incrementOdd", this.n)
           // },
           // incrementWait() {
           //     this.$store.dispatch("incrementWait", this.n);
           // }
           //借助mapActions生成对应方法
           ...mapActions(["incrementOdd", "incrementWait"])
       }
   }
   </script>
   ```

   **注意：mapMutations和mapActions使用时，若需要传递参数，需要在模板中绑定事件就传递好参数，否则参数时事件对象**

## 三，Vuex的模块化

1. 按需要将actions，mutations，state分类，写在不同的文件中

   - count.js

     ```js
     export default {
         //必须加上开启命名空间
         namespaced: true,
         actions: {
             incrementOdd(context, n) {
                 if (context.state.sum % 2 != 0) {
                     context.commit("INCREMENT", n);
                 }
             },
             incrementWait(context, n) {
                 setTimeout(() => {
                     context.commit("INCREMENT", n);
                 }, 500);
             }
         },
         mutations: {
             INCREMENT(state, n) {
                 state.sum += n;
             },
             DECREMENT(state, n) {
                 state.sum -= n;
             }
         },
         state: {
             sum: 0
         }
     }
     ```

   - person.js

     ```js
     export default {
         namespaced: true,
         actions: {
     
         },
         mutations: {
             ADD_PERSON(state, person) {
                 state.personList.unshift(person);
             }
         },
         state: {
             personList: [
                 {id: "001 ", name: "wgx"}
             ]
         }
     }
     ```

2. 在index.js中引入所有的模块文件

   ```js
   //该文件用于创建Vuex中最为核心的store
   
   //引入Vuex
   import Vuex from "vuex";
   //引入Vue
   import Vue from "vue";
   //引入person相关操作
   import personAbout from "@/store/person";
   //引入count相关操作
   import countAbout from "@/store/count";
   
   //使用Vuex
   Vue.use(Vuex);
   
   
   
   //创建并暴露store
   export default new Vuex.Store({
       modules: {
           personAbout, countAbout
       }
   });
   ```

3. 修改组件的相关写法

   - count.vue

     ```vue
     <script>
     import {mapState, mapMutations, mapActions} from "vuex";
     export default {
         name: "Count",
         data() {
             return {
                 n: 1
             }
         },
         computed: {
             // sum() {
             //     return this.$store.state.sum;
             // },
             //等价于上面
             ...mapState("countAbout", ["sum"]),
             ...mapState("personAbout", ["personList"])
         },
         methods: {
             // increment() {
             //     this.$store.commit("INCREMENT", this.n);
             // },
             // decrement() {
             //     this.$store.commit("DECREMENT", this.n);
             // },
             //借助mapMutations生成对应方法
             ...mapMutations("countAbout", {increment: "INCREMENT", decrement: "DECREMENT"}),
             // incrementOdd() {
             //     this.$store.dispatch("incrementOdd", this.n)
             // },
             // incrementWait() {
             //     this.$store.dispatch("incrementWait", this.n);
             // }
             //借助mapActions生成对应方法
             ...mapActions("countAbout", ["incrementOdd", "incrementWait"])
         }
     }
     </script>
     ```

   - person.vue

     ```vue
     <script>
     import {mapState} from "vuex";
     import {nanoid} from "nanoid";
     
     export default {
         name: "Person",
         data() {
             return {
                 name: ""
             }
         },
         computed: {
             ...mapState("personAbout", ["personList"])
         },
         methods: {
             add() {
                 const person = {id: nanoid(), name: this.name};
                 this.$store.commit("personAbout/ADD_PERSON", person);
                 this.name = "";
             }
         }
     }
     </script>
     ```

# 第五章 vue-router

## 一，相关理解

1. vue-router的理解

   vue的一个插件库，专门用来实现SPA应用

2. 对SPA应用的理解

   - 单页Web应用（single page web application）
   - 整个应用只有一个完整的页面
   - 点击页面中的导航链接不会刷新页面，只会做页面的局部更新
   - 数据需要通过ajax请求获取

3. 路由的理解

   - 一个理由就是一组映射关系（key-value）
   - key为路径，value可能是function或者component

4. 路由的分类

   - 后端路由
     - 理解value是function，用于处理客户端提交的请求
     - 工作过程：服务器接受到一个请求时，根据请求路径找到匹配的函数来处理请求，返回响应数据
   - 前端路由
     - 理解：value是component，用于展示页面的内容
     - 工作过程：当浏览器的路径改变时，对应的组件就会显示

## 二，基本路由

1. 安装vue-router

   npm i vue-router@3（vue2必须使用vue-router3）

2. 应用插件：Vue.use(VueRouter);

3. 在src/router编写router配置，index.js

   ```js
   //该文件专门用于创建整个应用的路由器
   import VueRouter from "vue-router";
   //引入组件
   import About from "@/components/About";
   import Home from "@/components/Home";
   
   //创建并暴露一个路由器
   export default new VueRouter({
       routes: [
           {
               path: "/home",
               component: Home
           },
           {
               path: "/about",
               component: About
           }
       ]
   })
   ```

4. 实现切换（active-class可以配置高亮样式）

   ```html
   <router-link class="list-group-item" active-class="active" to="/about">About</router-link>
   <router-link class="list-group-item" active-class="active" to="/home">Home</router-link>
   ```

5. 指定展示位置

   ```html
   <!--指定组件的呈现位置-->
   <router-view></router-view>
   ```

## 三，嵌套（多级）路由

1. ```js
   //创建并暴露一个路由器
   export default new VueRouter({
       routes: [
           {
               path: "/home",
               component: Home,
               //通过children配置子级路由
               children: [
                   {
                       path: "news",//不能写成/news
                       component: News
                   },
                   {
                       path: "message",//不能写成/message
                       component: Message
                   }
               ]
           },
           {
               path: "/about",
               component: About
           }
       ]
   })
   ```

2. ```vue
   <template>
       <div>
           <h2>我是Home的内容</h2>
           <div>
               <ul class="nav nav-tabs">
                   <!--跳转的时候需要写完整路径-->
                   <li><router-link class="list-group-item" active-class="active" to="/home/news">News</router-link></li>
                   <li><router-link class="list-group-item" active-class="active" to="/home/message">Message</router-link></li>
               </ul>
           </div>
           <router-view></router-view>
       </div>
   </template>
   ```

## 四，路由的参数

1. query参数

   ```vue
   <template>
       <div>
           <ul>
               <li v-for="message in messageList" :key="message.id">
                   <!-- 跳转路由并携带query参数，to的字符串写法 -->
                   <!--<router-link :to="`/home/message/detail?id=${message.id}&title=${message.title}`">{{message.title}}</router-link>-->
                   <!-- 跳转路由并携带query参数，to的对象写法 -->
                   <router-link :to="{
                       path: '/home/message/detail',
                       query: {
                           id: message.id,
                           title: message.title
                       }
                   }">{{message.title}}</router-link>
               </li>
           </ul>
           <hr>
           <router-view></router-view>
       </div>
   </template>
   ```

2. params参数

   - 配置路由时需要声明接收params参数

     ```js
     //创建并暴露一个路由器
     export default new VueRouter({
         routes: [
             {
                 path: "/home",
                 component: Home,
                 //通过children配置子级路由
                 children: [
                     {
                         path: "news",//不能写成/news
                         component: News
                     },
                     {
                         path: "message",//不能写成/message
                         component: Message,
                         children: [{
                             name: "detail",//给路由命名
                             path: "detail/:id/:title",//声明接收params参数
                             component: Detail
                         }]
                     }
                 ]
             },
             {
                 path: "/about",
                 component: About
             }
         ]
     });
     ```

   - 传递参数

     ```html
     <!-- 跳转路由并携带params参数，to的字符串写法 -->
     <!--<router-link :to="`/home/message/detail/${message.id}/${message.title}`">{{message.title}}</router-link>-->
     <!-- 跳转路由并携带params参数，to的对象写法 -->
     <router-link :to="{
         name: 'detail',
         //path: '/home/message/detail',(不能使用path)
         params: {
             id: message.id,
             title: message.title
         }
     }">{{message.title}}</router-link>
     ```

   - 接收参数

     ```vue
     <template>
         <div>
             <ul>
                 <li>消息编号:{{ $route.params.id }}</li>
                 <li>消息标题:{{ $route.params.title }}</li>
             </ul>
         </div>
     </template>
     ```

   - **注意：路由携带params参数时，若使用to的对象写法，则不能使用path配置项，必须使用name配置**

## 五，命名路由

1. 作用：简化路由的跳转

2. 给路由命名

   ```js
   //创建并暴露一个路由器
   export default new VueRouter({
       routes: [
           {
               path: "/home",
               component: Home,
               //通过children配置子级路由
               children: [
                   {
                       path: "news",//不能写成/news
                       component: News
                   },
                   {
                       path: "message",//不能写成/message
                       component: Message,
                       children: [{
                           name: "detail",//给路由命名
                           path: "detail",
                           component: Detail
                       }]
                   }
               ]
           },
           {
               path: "/about",
               component: About
           }
       ]
   });
   ```

3. 简化跳转

   ```html
   <!-- 跳转路由并携带query参数，to的对象写法 -->
   <router-link :to="{
       name: 'detail',
       //path: '/home/message/detail',
       query: {
           id: message.id,
           title: message.title
       }
   }">{{message.title}}</router-link>
   ```

## 六，路由的props配置

```js
{
    name: "detail",//给路由命名
    path: "detail/:id/:title",//声明接收params参数
    component: Detail,
    //第一种写法：props值为对象，该对象中所有的key-value的组合最终都会通过props传给Detail组件
    // props: {id: "001", title:"消息001"}
    //第二种写法：props值为布尔值，布尔值为true,则把路由收到的所有params参数通过props传给Detail组件
    // props: true
    //第三种写法：props值为函数，该函数返回的对象中每一组key-value都会通过props传给Detail组件
    props($route) {
        return {
            id: $route.query.id,
            title: $route.query.title
        }
    }
}
```

## 七，router-link的replace属性

1. 作用：控制路由跳转时操作浏览器历史记录的模式
2. 浏览器的历史记录有两种写入方式：分别为push和replace，push是追加历史记录，replace是替换当前记录路由跳转时候默认为push
3. 如何开启replace模式：<router-link replace\>News\</router-link>

## 八，编程式路由导航

1. 作用：不借助<router-link\>实现路由跳转，让路由跳转更加灵活

2. 具体实现

   ```js
   methods: {
       pushShow(message) {
           this.$router.push({
               name: 'detail',
               //path: '/home/message/detail',
               params: {
                   id: message.id,
                   title: message.title
               }
           });
       },
       replaceShow(message) {
           this.$router.replace({
               name: 'detail',
               //path: '/home/message/detail',
               params: {
                   id: message.id,
                   title: message.title
               }
           });
       },
       back() {
           this.$router.back()
       },
       forward() {
           this.$router.forward();
       }
   }
   ```

## 九，缓存路由组件

1. 作用：让不展示的组件保持挂载，不被销毁

2. 具体实现

   ```html
   <keep-alive include="News">
       <router-view></router-view>
   </keep-alive>
   ```

## 十，两个新的生命周期钩子

1. 作用：路由组件独有的两个钩子，用于捕获路由组件的激活状态

2. 具体使用

   ```js
   export default {
       name: "News",
       activated() {
           console.log("News组件被激活");
       },
       deactivated() {
           console.log("News组件失活了");
       }
   }
   ```

## 十一，路由守卫

1. 作用：对路由进行权限控制

2. 分类：全局守卫、独享守卫、组件内守卫

3. 全局守卫

   ```js
   //全局前置路由守卫——初始化的时候被调用，每次路由切换之前被调用
   router.beforeEach((to, from , next) => {
       if (to.meta.isAuth) {
           if (localStorage.getItem("user") === "wgx") {
               next();
           } else {
               alert("没有权限");
           }
       } else {
           next();
       }
   });
   
   //全局前置路由守卫——初始化的时候被调用，每次路由切换之后被调用
   router.afterEach((to, from) => {
       document.title = to.meta.title || "vue_test";
   });
   ```

4. 独享守卫

   ```js
   {
       path: "news",//不能写成/news
       component: News,
       meta: {isAuth: true, title: "新闻"},
       beforeEnter(to, from , next) {
           if (to.meta.isAuth) {
               if (localStorage.getItem("user") === "wgx") {
                   next();
               } else {
                   alert("没有权限");
               }
           } else {
               next();
           }
       }
   },
   ```

5. 组件内守卫

   ```vue
   <template>
       <div>
           <h2>我是About的内容</h2>
       </div>
   </template>
   
   <script>
   export default {
       name: "About",
       beforeRouteEnter(to, from, next) {
           //通过路由规则进入该组件时被调用
           console.log("beforeRouteEnter");
           next();
       },
       beforeRouteLeave(to, from, next) {
           //通过路由规则离开该组件时被调用
           console.log("beforeRouteLeave");
           next();
       }
   }
   </script>
   ```







