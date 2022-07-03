# 第一章 Git的概述

## 一，Git概述

Git是一个免费的、开源的==分布式版本控制系统==，可以快速的高效的处理从小到大的各种项目

## 二，何为版本控制

版本控制是一种记录文件内容的变化，以便于来查询特定版本修订情况的系统

版本控制其实最重要的是可以记录文件修改历史记录，从而让用户能够查看历史版本，方便版本切换

## 三，版本控制工具

1. 集中式版本控制工具
2. 分布式版本控制工具

## 四，Git工作机制

![](F:\截图\屏幕截图 2022-01-26 195823.png)

## 五，Git和代码托管中心

代码托管中心是基于网络服务器的远程代码仓库，一般我们简单称之为远程库

- 局域网：GitLab
- 互联网：GitHub，Gitee

# 第二章 Git常用命令

![](F:\截图\屏幕截图 2022-01-26 201518.png)

## 一，设置用户签名

git init

![](F:\截图\屏幕截图 2022-01-27 172801.png)

## 二，添加到暂存区

git add 文件名

![](F:\截图\屏幕截图 2022-01-27 173623.png)

## 三，提交本地库

git commit -m "日志信息" 文件名

![](F:\截图\屏幕截图 2022-01-27 174014.png)

## 四，修改文件

![](F:\截图\屏幕截图 2022-01-27 175025.png)

## 五，版本穿梭

git reset --hard 版本号

![](F:\截图\屏幕截图 2022-01-27 175732.png)

# 第三章 分支操作

- 什么是分支

  在版本控制过程中，同时推进多个任务，为每一个任务，我们可以创建每个任务的单独分支。使用分支意味着程序员可以把自己的工作从主线上分离开来，开发自己分支的时候不会影响主线分支的运行

## 一，分支操作命令

![](F:\截图\屏幕截图 2022-01-27 194636.png)

# 第四章 GitHub

## 一，创建远程库别名

- 查看别名：git remote -v

- 创建别名：git remote add 别名 地址

  ![](F:\截图\屏幕截图 2022-01-27 202438.png)

## 二，推送本地库到远程库

git push 别名 分支名

![](F:\截图\屏幕截图 2022-01-27 204022.png)

## 三，拉取远程库到本地库

git pull 别名 分支名

![]()![屏幕截图 2022-01-27 204959](F:\截图\屏幕截图 2022-01-27 204959.png)

## 四，克隆远程库到本地库

git clone 连接

![](F:\截图\屏幕截图 2022-01-27 205553.png)

- clone会做如下操作

  1. 拉取代码

  2. 初始化本地库

  3. 创建别名

     ![](F:\截图\屏幕截图 2022-01-27 205809.png)

## 五，SSH免密登录

1. 在C盘用户目录下启动Git
2. 使用ssh-keygen -t rsa -C 2297665453@qq.com命令生成ssh key
3. 在GitHub上使用.ssh文件夹中id_rsa.pub中的key生成ssh链接

 # 第五章 git集成IDEA

## 一，环境准备

1. 忽略文件

   创建忽略规则文件XXX.ignore（建议git.ignore）

   这个文件存放位置原则上哪里都可以，为了便于让~/.gitconfig文件引用，建议也放在用户家目录下

   git.ignore文件模板

   ~~~ignore
   # compiled class file
   *.class
   
   # log file
   *.log
   
   # bluej files
   *.ctxt
   
   # mobile tools for java (j2me)
   *.mtj.tmp/
   
   #package file
   *.war
   *.jar
   *.ear
   *.nar
   *.zip
   *.tar.gz
   *.rar
   
   #kdiff3 ingnore
   *.orig
   
   #maven ignore
   taget/
   
   #eclipse ignore
   .settings/
   .project
   .classpath
   
   #idea
   .idea/
   /idea/
   *.ipr
   *.iml
   *.iws
   
   # temp file
   *.log
   *.cache
   *.diff
   *.tmp
   
   #system
   .DS_Store
   Thumbs.db
   ~~~

2. 在.gitconfig文件中引用忽略配置文件

   [core]
   	excludesfile = C:/Users/www22/git.ignore

3. 在IDEA中定位Git

   ![](F:\截图\屏幕截图 2022-01-28 164413.png)

# 第五章 Gitee

## 一，码云账号注册登录

![](F:\截图\屏幕截图 2022-01-29 195427.png)

## 二，Gitee集成IDEA

![](F:\截图\屏幕截图 2022-01-29 201007.png)
