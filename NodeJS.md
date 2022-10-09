# 一，读取文件和写入文件

1. 读取文件

   ```js
   let fs = require("fs");
   
   fs.readFile("./111.txt", "utf8", function (err, data) {
       console.log(data);
       console.log(err);
   });
   ```

2. 写入文件

   ```js
   let fs = require("fs");
   
   //文件路径 写入的数据 回调函数
   fs.writeFile("/111.txt", "123", function (err) {
       console.log(err);
   });
   ```

3. path模块

   ```js
   let fs = require("fs");
   let path = require("path");
   
   
   fs.readFile(path.join(__dirname, "/111.txt"), function (err, data) {
       console.log(data);
       console.log(err);
   });
   
   fs.writeFile(path.join(__dirname, "/111.txt"), "123", function (err) {
       console.log(err);
   });
   ```

# 二，创建服务器

1. 创建服务器

   ```js
   const http = require("http");
   
   const server = http.createServer();
   
   server.on("request", function (req, res) {
       console.log("server has request");
   });
   
   server.listen(80, function () {
       console.log("server running at 80");
   });
   ```

2. req请求对象和res响应对象

   ```js
   const http = require("http");
   
   const server = http.createServer();
   
   server.on("request", function (req, res) {
       console.log(req.url);
       console.log(req.method);
       res.end("ok");
   });
   
   server.listen(80, function () {
       console.log("server running at 80");
   });
   ```

3. 处理中文乱码

   ```js
   const http = require("http");
   
   const server = http.createServer();
   
   server.on("request", function (req, res) {
       console.log(req.url);
       console.log(req.method);
       res.setHeader("Content-Type", "text/html;charset-utf-8");
       res.end("成功");
   });
   
   server.listen(80, function () {
       console.log("server running at 80");
   });
   ```

# 三，模块化

1. 向外共享模块作用域中的成员

   在自定义模块中，可以使用module.exports对象，将模块内的成员共享出去，供外界使用。外界用require()方法导入自定义模块时，得到的就是==module.exports所指向的对象。==

   ```js
   module.exports.username = "wgx";
   ```

2. 导入自定义模块

   ```js
   const custom = require("./module");
   console.log(custom);
   ```

3. exports对象

   ```js
   exports.username = "wgx";
   ```

4. Node.js中的模块化规范

   - 每个模块内部，module变量代表当前模块。
   - module变量是个对象，它的exports属性（即module.exports）是对外的接口
   - 加载某个模块，其实是加载该模块的module.exports属性。require()方法用于加载模块。

# 四，npm与包

1. 使用npm -v查看npm包管理工具版本

2. npm install 包的完整名称（或者npm i 包的完整名称）

3. 使用

   ```js
   const moment = require("moment");
   
   const date = moment().format("YYYY-MM-DD hh:mm:ss");
   
   console.log(date);
   ```

4. 文件说明

   - node_modules文件夹用来存放所有已安装到项目中的包。require()导入第三方包时，就是从这个目录中查找并加载包。
   - package-lock.json配置文件用来记录node_modules目录下的每一个包的下载信息，例如包的名字、版本号、下载地址等。
   - 默认情况下，使用npm install命令安装包的时候，会自动安装最新版本的包。如果需要安装指定版本的包，可以在包名之后，通过@符号指定具体的版本。

5. 包管理配置文件

   npm规定，在项目根目录中，必须提供一个叫做package.json的包管理配置文件。用来记录与项目有关的一些配置信息。例如：

   - 项目的名称、版本号、描述等
   - 项目中都用到了哪些包
   - 哪些包只在开发期间会用到
   - 哪些包在开发和部署时都需要用到

   npm包管理工具提供了一个快捷命令，可以在执行命令时所处的目录中，快速创建package.json这个包管理配置文件：npm init -y

   注意：

   - 上述命令只能在英文的目录下成功运行！所以，项目文件夹的名称一定要使用英文命名，不要使用中文，不能出现空格。
   - 运行npm install命令安装包的时候，npm包管理工具会自动把包的名称和版本号，记录到package.json中。

   ==可以运行npm install命令（或npm i）一次性安装所有的依赖包==

   可以运行npm uninstall命令，来卸载指定的包

# 五，express

1. 安装express

   npm i express@4.17.1

2. 创建服务器

   ```js
   const express = require("express");
   
   const app = express();
   
   app.listen(80, () => {
       console.log("express server running at 80");
   });
   ```

3. 监听get和post请求

   ```js
   const express = require("express");
   
   const app = express();
   
   app.listen(80, () => {
       console.log("express server running at 80");
   });
   
   app.get("/", function (req, res) {
       res.send("get ok");
   });
   
   app.post("/", function (req, res) {
       res.send("post ok");
   });
   ```

4. 获取url中携带的参数

   ```js
   app.get("/query", function (req, res) {
       res.send(req.query);
   });
   ```

5. 获取路径中的参数

   ```js
   app.get("/params/:username", (req, res) => {
       res.send(req.params);
   });
   ```

6. 托管静态资源

   express提供了一个非常好用的函数，叫做express.static0,通过它，我们可以非常方便地创建一个静态资源服务器，例如，通过如下代码就可以将public目录下的图片、CSS文件、JavaScript文件对外开放访问了：

   ```js
   app.use(express.static("public"));
   ```

   可以访问http://localhost:3000/images/bg.jpg、http://localhost:3000/css/style.css、http://localhost:3000/js/login.js等静态资源

   **注意：Express在指定的静态目录中查找文件，并对外提供资源的访问路径。因此，存放静态文件的目录名不会出现在URL中。**

   如果希望在托管的静态资源访问路径之前，挂载路径前缀，则可以使用如下的方式：

   ```js
   app.use(express.static("/public", "public"));
   ```

   可以访问http://localhost:3000/public/images/bg.jpg、http://localhost:3000/public/css/style.css、http://localhost:3000/public/js/login.js等静态资源

7. 路由

   - 创建路由模块

     ```js
     const express = require("express");
     
     const router = express.Router();
     
     router.get("/", function (req, res) {
         res.send("get ok");
     });
     
     router.post("/", function (req, res) {
         res.send("post ok");
     });
     
     module.exports = router;
     ```

   - 导入路由模块

     ```js
     const express = require("express");
     const router = require("./router");
     
     const app = express();
     
     app.listen(80, () => {
         console.log("express server running at 80");
     });
     
     app.use(router);
     ```

# 六，跨域

1. CORS响应头部Access-Control-Allow-Origin

   如果指定了Access-Control-Allow-Origin字段的值为通配符*，表示允许来自任何域的请求，示例代码如下：

   ```js
   res.setHeader("Access-Control-Allow-Origin", "*");
   ```

2. CORS响应头部Access-Control-Allow-Headers

   默认情况下，CORS仅支持客户端向服务器发送如下的9个请求头：Accept、Accept-Language、Content-Language、DPR、Downlink、Save-Data、Viewport-Width、Width、Content-Type（值仅限于text/plain、multipart/form-data、application/x-www-form-urlencoded三者之一）如果客户端向服务器发送了额外的请求头信息，则需要在服务器端，通过Access-Control-Allow-Headers对额外的请求头进行声明，否则这次请求会失败！

   ```js
   res.setHeader("Access-Control-Allow-Headers", "Content-Type, X-Custom-Header");
   ```

3. Access-Control-Alow-Methods

   默认情况下，CORS仅支持客户端发起GET、POST、HEAD请求。如果客户端希望通过PUT、DELETE等方式请求服务器的资源，则需要在服务器端，通过Access-Control-Alow-Methods来指明实际请求所允许使用的HTTP方法。示例代码如下：

   ```js
   res.setHeader("Access-Control-Alow-Methods", "POST, GET, DELETE, HEAD");
   //允许所有的HTTP请求方法
   res.setHeader("Access-Control-Alow-Methods", "*");
   ```

   

