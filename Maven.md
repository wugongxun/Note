# 第一章 Maven简介

1. Maven是一款服务于Java平台的自动化构建工具

   - 构建：构建是面向过程的，就是一些步骤，完成项目代码的编译，测试，运行，打包，部署等

   - Maven支持的构建包括

     - 清理：把之前项目编译的东西删除掉，为新的编译代码做准备

     - 编译：把程序源代码编译为执行代码，java->class文件

       ​			Maven可以同时把成千上万个文件编译为class文件

     - 测试：Maven可以执行测试程序代码，验证你的功能是否正确

       ​			Maven可以同时执行多个测试代码，同时测试很多功能

     - 报告：生成测试结果文件

     - 打包：把你的项目中所有的class文件，配置文件等所有资源放到一个压缩文件中，这个         压缩文件就 是项目的结果文件，通常java程序压缩文件是jar扩展名的，对于web应用，      压缩文件扩展名是war

     - 安装：把生成的结果文件安装到本机仓库

     - 部署：把程序安装好，可以执行

2. Maven的核心概念

   - POM：一个文件，名称是pom.xml，pom翻译过来叫项目对象模型

     ​			Maven把一个项目当做一个模型使用，控制Maven构建项目的过程，管理jar依赖

   - 约定的目录结构：Maven项目的目录和文件的位置都是规定的

   - 坐标：是一个唯一的字符串，用来表示资源的

   - 依赖管理：管理你的项目可以使用jar文件

   - 仓库管理：你的资源存放的位置

   - 生命周期：Maven工具构建项目的过程就是生命周期

   - 插件和目标：执行Maven构建项目的时候用的工具是插件

   - 继承

   - 聚合

# 第二章 Maven的使用

1. Maven中约定的目录结构

   ![](F:\截图\捕获147.PNG)

   Hello：根目录，也就是工程名

   src：源代码

   main：主程序

   - java：主程序的源码
   - resources：主程序的配置文件

   test：测试程序

   - java：测试程序的源码
   - resources：测试程序的配置文件

   pom.xml：Maven的核心文件

2. 修改本机存放资源的目录位置

   - 修改Maven的配置文件，Maven安装目录/conf/setting.xml（建议先备份一下）
   - 修改\<localRepository>，指定你的目录（不要使用中文目录）

3. 仓库的分类

   - 本地仓库：就是你的个人计算机上的文件夹，存放各种jar包
   - 远程仓库：在互联网上的，使用网络才能使用的仓库
     - 中央仓库：最权威的，所有开发人员都共享使用的一个集中仓库
     - 中央仓库的镜像：就是中央仓库的备份，在各大洲，重要城市都是镜像
     - 私服：在公司内部，在局域网中使用的，不是对外使用的

4. POM文件：项目对象模型，是一个pom.xml文件

   - 坐标：唯一值，在互联网中唯一标识一个项目的

     ```xml
     <groupId>公司域名的倒写</groupId>
     <artifactId>自定义项目的名称</artifactId>
     <version>自定义版本号</version>
     ```

   - packageing：打包后压缩文件的扩展名，默认是jar，web应用是war

   - 依赖：dependencies和dependency，相当于java代码中import

   - properties：设置属性

   - build：maven在进行项目的构建时，配置信息

5. maven的生命周期：就是maven构建项目的过程，清理、编译、测试、报告、打包、安装、部署

6. maven的命令：maven独立使用，通过命令，完成maven的生命周期的执行

   ​							maven可以使用命令完成项目的清理，编译，测试等等

   - mvn clean：清理（会删除原来的编译和测试的目录，即target目录，但是已经install到仓库里的包不会删除）
   - mvn compile：编译主程序（会在当前目录下生成一个target，存放编译主程序之后生成的字节码文件）
   - mvn test-compile：编译测试程序（会在当前目录下生成一个target，存放测试程序编译之后生成的字节码文件）
   - mvn test：测试（会生成一个目录surefire-reports，保存测试结果）
   - mvn package：打包主程序（编译、编译测试、测试，并按照pom.xml配置把主程序打包生成jar包或者war包）
   - mvn install：安装主程序（会把本工程打包，并按照本工程的坐标保存到本地仓库中）
   - mvn deploy：部署主程序（会把本工程打包，按照本工程的坐标保存到本地库中，并且还会保存到私服仓库中，还会自动把项目部署到web容器中）

7. maven的插件：maven命令执行时，真正完成功能的是插件，插件就是一些jar文件

# 第三章 Maven在IDEA中的应用

1. 在IDEA中设置maven，IDEA中内置了maven，一般不使用内置的，使用自己安装的maven，需要覆盖IDEA中的默认的设置，让IDEA指定maven的安装位置等信息

   ![](F:\截图\捕获148.PNG)

2. 使用模板创建项目

   - maven-archetype-quickstart：普通的java项目
   - maven-archetype-webapp：web项目

3. scope表示依赖范围

   ```xml
   <dependency>
     <groupId>junit</groupId>
     <artifactId>junit</artifactId>
     <version>4.11</version>
     <scope>test</scope>
   </dependency>
   ```

   scope值有compile，test，provided，默认值compile

   scope表示依赖使用的范围，也就是在maven构建项目的那些阶段中起作用

   ![](F:\截图\捕获149.PNG)

4. 全局变量

   自定义的属性：在<properties\>通过自定义标签声明变量（标签名就是变量名）

   ​						   在pom.xml文件中的其他文职使用${标签名}使用变量的值

   全局变量一般是定义依赖的版本号，当你的项目中要使用多个相同的版本号，先使用全局变量定义，在使用${变量名}

5. 资源插件

   - 默认没有使用resources的时候，maven执行编译代码时，会把src/main/resources目录中的 文件拷贝到target/classses目录中。对于src/main/java目录下的非java文件不处理
   - 我们的程序有需要把一些文件放在src/main/java目录中，当我们在执行java程序时，需要用到src/main/java目录中的文件。此时就需要在<build\>中加入\<resources>

   