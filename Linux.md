# 第一章 Linux基础

## 一，Linux目录结构

1. 基本介绍
   - Linux的文件系统是采用级层式的树状目录结构，在此结构中的最上层是根目录`/`，然后在此目录下在创建其他目录
   - 在Linux里一切皆文件
   - ![](F:\截图\屏幕截图 2022-02-27 210035.png)

2. 具体的目录结构

   - /bin

     是Binary的缩写，这个目录存放着最经常使用的命令

   - /sbin

     s就是Super User的意思，这里存放的是系统管理员使用的系统管理程序

   - /home

     存放普通用户的主目录，在Linux中每一个用户都有一个自己的目录，一般目录名是以用户的账号命名

   - /root

     该目录为系统管理员，也称为超级权限用户主目录

   - /lib

     系统开机所需要的最基本的动态连接共享库，其作用类似于Windows里的DLL文件，几乎所有的应用程序都需要用到这些共享库

   - /lost+found

     这个目录一般情况下是空的，当系统非法关机后，这里就存放了一些文件

   - /etc

     所有的系统管理所需要的配置文件和子目录

   - /usr

     用户的很多应用程序和文件都放在这个目录下，类似于Windows下的program files目录

   - /boot

     存放的是启动Linux时使用的一些核心文件，包括一些连接文件以及镜像文件

   - /proc

     这个目录是一个虚拟的目录，它是系统内存的映射，访问这个目录来获取系统信息

   - /srv

     service的缩写，该目录存放一些服务启动之后所需要提取的数据

   - /sys

     这个Linux2.6内核的一个很大的变化，该目录下安装了2.6内核中新出现的一个文件系统sysfs

   - /tmp

     这个目录用来存放一些临时文件

   - /dev

     类似于Windows的设备管理器，把所有的硬件用文件的形式储存

   - /media

     Linux系统会自动识别一些设备，例如U盘、光驱等等，当识别后，Linux会把识别的设备挂载到这个目录下

   - /mnt

     系统提供该目录是为了让用户临时挂载别的文件系统的，我们可以将外部的储存挂载载/mnt上，然后进入该目录就可以查看里面的内容

   - /opt

     这是给主机额外软件存放的目录，如安装oracle数据库就可以放在该目录下。默认为空

   - /usr/local

     这是另一个给主机额外安装软件所安装的目录，一般是通过编译源码的方式安装程序

   - /var

     这个目录中存放着不断扩充的东西，习惯将被经常修改的目录放在这个目录下，包括各种日志文件

   - /selinux [security-enhanced-linux]

     SELinux是一种子系统，它能控制程序只能访问特定的文件，有三种工作模式，可以自行设置

## 二，vi和vim基本使用

1. 各种模式的相互切换

   ![](F:\截图\屏幕截图 2022-02-28 142225.png)

2. 快捷键

   ![](F:\截图\屏幕截图 2022-02-28 142636.png)

   ![](F:\截图\VIM 键盘图.gif)

## 三，关机&重启命令

![](F:\截图\屏幕截图 2022-02-28 143612.png)

## 四，用户登录和注销

![](F:\截图\屏幕截图 2022-02-28 144527.png)

## 五，用户管理

1. 添加用户

   useradd 用户名

   当创建用户成功后，会自动创建和用户同名的home目录

   也可以使用**useradd -d 指定目录 新用户名**，给新创建的用户指定home目录

2. 设置密码

   passwd 用户名

3. 删除用户

   userdel 用户名：删除用户，但是不删除home目录

   userdel -r 用户名：删除用户，并把home目录删除

4. 查看用户信息

   id 用户名

5. 查看当前登录用户信息

   who am i

6. 用户组

   - 新增组：groupadd 组名
   - 删除组：groupdel 组名
   - 增加组的时候直接加上组：useradd -g 用户组 用户名
   - 修改用户的组：usermod -g 用户组 用户名

## 六，常用指令

1. 指定运行级别

   init[0,1,2,3,4,5,6]

   0：关机

   1：单用户【找回丢失密码】

   2：多用户状态没有网络服务

   3：多用户状态有网络服务

   4：系统未使用保留给用户

   5：图形界面

   6：系统重启

   systemctl get-default：查看当前默认的运行级别

   systemctl get-default：设置默认的运行级别

2. 帮助指令

   man：获得帮助信息

   help：获得shell内置命令的帮助信息

3. 文件目录类

   - pwd：显示当前位置的绝对路径

   - mkdir：创建目录

     mkdir -p：创建多级目录

   - rmdir：指令删除空目录

     rm -rf：删除非空目录

   - touch：创建一个空文件

   - cp source  dest：拷贝文件到指定目录

     cp -r：递归复制整个文件夹

     \cp -r：强制覆盖不提示

   - rm 要删除的文件或者目录：删除文件或者目录

     rm -rf：强制递归删除

   - mv oldNameFile newNameFile：重命名

     mv /temp/moveFile /tagetFolder：移动

   - cat ：查看文件内容

     cat -n：显示行号

   - more：more是一个基于VI编辑器的文本过滤器，它以全屏幕的方式按页显示文本文件的内容，more指令中内置了若干快捷键

     ![](F:\截图\屏幕截图 2022-02-28 210355.png)

   - less：less指令用来分屏查看文件内容，它的功能与more指令类似，但是比more指令更加强大，支持各种显示终端。less指令在显示文件内容时，并不是一次将整个文件加载之后才显示，而是根据显示需要加载内容，对于显示大型文件具有较高的效率

     ![](F:\截图\屏幕截图 2022-02-28 210744.png)

   - echo：输出内容到控制台

     例 echo $PATH：在控制台输出环境变量

     例 echo "hello world~"：在控制台输出hello world~

   - head：head用于显示文件的开头部分，默认显示文件前十行

     head -n 5：显示文件前5行

   - tail：tail用于输出文件中的尾部内容，默认显示前十行

     tail -n 5：显示文件后5行

     tail -f：实时跟踪该文件的所有更新

   - \>指令和\>>

     \>：输出重定向（覆盖）

     \>>：追加

   - ln -s：软链接也称为符号链接，类似于Windows里的快捷方式，主要存放了链接其他文件的路径
   
   - history：查看已经执行过的命令
   
     history 10：显示最近使用过的10条指令
   
     history !5：执行历史编号为5的指令
   
4. 时间日期类

   - date：显示当前日期

     date +%Y：显示当前年份

     date +%m：显示当前月份

     date +%d：显示当前是哪一天

     date "+%Y-%m-%d %H:%M:%S"：显示年-月-日 时:分:秒

     date -s 字符串时间：设置系统当前时间

   - cal：显示本月日历

     cal 2022：显示2022年日历

5. 搜索查找类

   - find：从指定目录下递归的遍历其各个子目录，将满足条件的文件或者目录显示在终端

     find -name：按照指定的文件名查找文件

     find -user：查找属于指定用户名所有文件

     find -size (+n大于 -n小于 n等于)：按照指定的文件大小查找文件

   - locate：locate指令可以快速定位文件路径，locate指令利用事先建立的系统中所有文件名称路径的locate数据库实现快速定位给定的文件。locate指令无需遍历整个文件系统，查询速度较快，为了保证查询结果的准确度，管理员必须顶起更新locate时刻

   - which：可以查看某个指令在哪个目录下

   - grep：过滤查找

     grep -n：显示匹配行及行号

     grep -i：忽略大小写

   - 管道符“|”：表示将前一个命令的结果输出传递给后面的命令处理

6. 压缩和解压类

   - gzip：压缩文件，只能将文件压缩为*.gz文件

   - gunzip：解压缩文件

   - zip：压缩文件

     zip -r：递归压缩，即压缩目录

   - unzip：解压文件

     unzip -d ：指定解压后文件的存放目录

   - tar：打包指令，最后打包后的文件是*.tar.gz

     ![](F:\截图\屏幕截图 2022-03-01 130513.png)

     压缩示例 tar -zcvf test.tar.gz Hello.java a.txt

     解压示例 tar -zxvf test.tar.gz

     ==tar -zxvf test.tar.gz -C <指定解压后存放的目录>==

## 七，组

1. 基本概念

   在Linux中每一个用户都必须有一个组，不能独立于组外

   在Linux中每个文件有所有者，所在组，其他组的概念

2. 所有者

   - 查看文件所有者：ls -ahl

   - 修改文件所有者：chown 用户名 文件名

     chown -R 如果是目录，递归修改目录下所有子文件或目录的所有者

3. 所在组

   - 组创建：groupadd 组名

   - 修改文件所在组：chgrp 组名 文件名

     chgrp -R 如果是目录，递归修改目录下所有子文件或目录的所在组

4. 其他组

   - 除文件的所有者和所在组的用户外，系统的其他用户都是文件的其他组

   - 修改用户所在组

     usermod -g 新组名 用户名

     usermod -d 目录名 用户名 改变该用户登录初始目录（用户需要有进入到新目录的权限）

## 八，权限

-rw-r--r-- 1 wgx  wgx   99 Feb 28 14:42 Hello.java

[-]第0位确定文件类型（d,-,l,c,b）

- l是链接
- d是目录
- c是字符设备文件，鼠标，键盘
- b是块设备，比如硬盘

[rw-]第1-3位确定所有者（该文件的所有者）拥有该文件的权限--User

[r--]第4-6位确定所属组（同组的用户）拥有该文件的权限--Group

[r--]第7-9位确定其他用户拥有该文件的权限--Other

[1]第10位表示子目录数，如果是文件就为1

[wgx]第11位表示用户

[wgx]第12位表示组

[99]第13位表示文件大小

[Feb 28 14:42]第14位表示最后的修改日期

[Hello.java]第15位表示文件名

1. rwx权限（文件）

   r：可以读取，查看

   w：可以修改，但是不代表可以删除，删除一个文件的前提是对==该文件所在的目录==有写权限，才能删除该文件

   x：可以执行

2. rwx权限（目录）

   r：可以读取，ls查看目录内容

   w：可以修改，对目录内创建+删除+重命名

   x：可以执行，可以进入该目录

3. 修改权限

   - 使用+，-，=

     chmod u=rwx,g=rw,o=r 文件/目录

     chmod o+w 文件/目录

     chmod u-x 文件/目录

     chmod a+r 文件/目录

   - 使用数字

     r=4 w=2 x=1

     chmod 764 文件/目录

## 九，定时任务调度

1. 基本概念

   任务调度：是指系统在某个时间执行的特定的命令或程序

   任务调度分类：

   - 系统工作：有些重要的工作必须周而复始的执行
   - 个别用户工作：个别用户可能希望执行某些程序

2. crontab：进行定时任务的设置

   ![](F:\截图\屏幕截图 2022-03-01 144354.png)

3. 设置个人任务调度

   例 */1 * * * * ls -l /etc/ > /tmp/to.txt

   ![](F:\截图\屏幕截图 2022-03-01 145008.png)

   ==特殊符号说明==

   ![](F:\截图\屏幕截图 2022-03-01 145343.png)

   重启任务调度：service crond restart

4. at定时任务

   - at命令是一次性定时计划任务，at的守护进程atd会以后台模式运行，检查作业队列来运行

   - 默认情况下，atd守护进程每60秒检查作业队列，有作业时，会检查作业运行时间，如果时间与当前时间匹配，则运行此作业

   - at命令是一次性定时计划任务，执行完一个任务后不再执行此任务

   - 在使用at命令时，一定要保证atd进程的启动，可以使用相关指令来查看

     ps -ef 可以查看所有在运行的进程

   - 基本语法：at [选项\] [时间\]

   ### at命令选项

   ![](F:\截图\屏幕截图 2022-03-01 151230.png)

   ### at时间定义

   ![](F:\截图\屏幕截图 2022-03-01 151441.png)

   atq：查看系统中有没有需要执行的工作任务

   atrm：删除已经设置的任务

## 十，磁盘分区，挂载

1. 原理介绍

   - Linux无论有几个分区，分给哪一个目录使用，它归根结底只有一个根目录，一个独立且唯一的文件结构，Linux中每个分区都是用来组成整个文件系统的一部分
   - Linux采用了一种叫“载入”的处理方法，它的整个文件系统中包含了一整套的文件和目录，且将一个分区和一个目录联系起来，这时要载入一个分区将使它的存储空间在一个目录下获得

2. 硬盘说明

   - Linux硬盘分IDE硬盘和SCSI硬盘，目前基本上是SCSI硬盘
   - 对于IDE硬盘，驱动标识符为“hdx~”，其中“hd”表明分区所在设备的类型，这里是指IDE银盘。“x”为盘号（a为基本盘，b为基本从属盘，c为辅助主盘，d为辅助从属盘），“~”代表分区，前四个分区用数字1到4表示，它们是主分区或扩展分区，从5开始就是逻辑分区。例，hda3表示第一个IDE硬盘上的第三个主分区或扩展分区，hdb2表示为第二个主分区或扩展分区
   - 对于SCSI硬盘则标识为“sdx~”，SCSI硬盘是用“sd“来表示分区所在设备的类型的，其余则和IDE硬盘的表示方法一样

3. 查看所有设备挂载情况

   lsblk或者lsblk -f

4. 增加一块硬盘

   - 添加一块硬盘

   - 分区

     fdisk /dev/sdb：分区命令

     m：显示命令列表

     p：显示磁盘分区，同fdisk -l

     n：新增分区

     d：删除分区

     w：写入并退出

   - 格式化磁盘

     mkfs -t ext4 /dev/sdb1

     ext4是分区类型

   - 挂载/卸载

     挂载：mount /dev/sdb1 要挂载的目录

     卸载：umount /dev/sdb1

   - 永久挂载

     通过修改/etc/fstab实现挂载

     ![](F:\截图\屏幕截图 2022-03-02 195241.png)

5. 磁盘情况查询

   - 查询系统整体磁盘使用情况：df -h

   - 查询指定目录的磁盘占用情况：du -h 指定目录

     **默认查询当前目录**

     -s：指定目录占用大小

     -h：带计量单位

     -a：含文件

     --max-depth=1：子目录深度

     -c：列出明细的同事，增加汇总值

6. 查看磁盘情况常用命令

   - 统计/homet文件夹下文件的个数

     ls -l /home | grep "^-" | wc -l

   - 统计/home文件夹下目录的个数

     ls -l /home | grep "^d" | wc -l

   - 统计/home文件夹下文件的个数，包括子文件夹里的

     ls -lR /home | grep "^-" | wc -l

   - 统计/home文件夹下目录的个数，包括子文件夹里的

     ls -lR /home | grep "^d" | wc -l

   - 以树状显示目录结构

     tree 目录

# 第二章 网络配置

1. 指定ip地址

   直接修改配置文件来指定ip，并可以连接到外网

   vim /etc/sysconfig/network-scripts/ifcfg-eth0（阿里云服务器）

   - ![](F:\截图\屏幕截图 2022-03-02 204518.png)

   - 按照如下信息修改

     BOOTPROTO=static

     #IP地址

     IPADDR=192.168.xxx.xxx

     #网关

     GATEWAY=192.168.xxx.xxx

     #域名解析器

     DNS1=192.168.xxx.xxx

   - 重启

     reboot, service network restart
   
2. 主机名和hosts映射

   - 查看主机名：hostname

   - 修改主机名：vim /etc/hostname

   - 设置hosts映射

     - Windows

       在C:\Windows\System32\drivers\etc\hosts 文件指定即可

     - Linux

       在/etc/hosts 文件指定

   - 什么是Hosts

     一个文本文件，用来记录IP和Hostname（主机名）的映射关系

   - DNS

     DNS就是Domain Name System，域名系统

     是互联网上作为域名地址相互映射的一个分布式数据库

     ipconfig /displaydns：DNS域名解析缓存

     ipconfig /flushdns：手动清理dns缓存

# 第三章 进程管理

## 一，基本介绍

1. 在Linux中每个执行的程序都称为一个进程，每一个进程都分配一个ID号（pid，进程号）
2. 每个进程都可能以两种方式存在的，前台与后台，所谓前台进程就是用户目前的屏幕上可以进行操作的。后台进程就是实际在操作，但由于屏幕上无法看到的进程，通常使用后台的方式执行
3. 一般系统的服务都是以后台进程的方式存在，而且都会常驻在系统中

## 二，显示系统执行的进程

1. ps命令时用来查看目前系统中，有哪些正在执行，以及他们的执行状况，可以不加参数

2. 显示的信息选项

   ![](F:\截图\屏幕截图 2022-03-03 145508.png)

3. 参数选项

   <img src="F:\截图\屏幕截图 2022-03-03 145607.png" style="zoom:150%;" />

4. ps详解

   ![](F:\截图\屏幕截图 2022-03-03 150114.png)

## 三，父子进程

ps -ef：以全格式显示当前所有进程，查看进程的父进程

- -e：显示所有进程
- -f：全格式

## 四，终止进程

kill 进程号：终止进程

killall 进程名：通过进程名称杀死进程，支持通配符

- -9：表示强迫进程立即停止

## 五，查看进程树

pstree：可以更加直观的来查看进程信息

- -p：显示进程PID
- -u：显示进程的所属用户

## 六，动态监控系统

top指令

- top指令与ps指令很相似，他们都是用来显示正在执行的进程，top与ps最大的不同之处，在于top在执行一段时间可以更新正在运行的进程

- 基本语法：top [选项]

- ![](F:\截图\屏幕截图 2022-03-04 132922.png)

- 交互操作

  ![](F:\截图\屏幕截图 2022-03-04 133317.png)

## 七，监控网络状态

- 查看系统网络情况：netstat
- netstat -an：按一定的顺序排列输出
- netstat -p：显示那个进程在调用

# 第四章 服务管理

## 一，介绍

服务（service）本质就是进程，但是是运行在后台的，通常都会监听某个端口，等待其他程序的请求，因此我们又称为守护进程

## 二，service管理指令

- service 服务名 [start | stop | restart | reload | status]
- 在CentOS7.0以后==很多服务不再使用service，而是systemctl==

## 三，查看服务名

1. setup：查看系统服务
2. 查看/etc/init.d

## 四，服务的运行级别

- 0：系统停机状态，系统默认运行级别不能设置为0，否则不能正常启动
- 1：单用户工作状态，root权限，用户系统维护，禁止远程登录
- 2：多用户状态（没有NFS），不支持网络
- 3：完全多用户状态（有NFS），登录以后进入控制台命令行模式
- 4：系统未使用，保留
- 5：X11控制台，登录以后进入图形GUI模式
- 6：系统正常关闭重启，默认运行级别不能设置为6，否则不能正常启动

### 开机的流程

![](F:\截图\屏幕截图 2022-03-03 203939.png)

## 五，服务管理

1. chkconfig指令
   - chkconfig指令可以给服务的各个运行级别设置自启动/关闭
   - chkconfig指令管理的服务在/etc/init.d查看
   - 在CentOS7.0以后，很多服务使用systemctl管理
2. chkconfig指令语法
   - 查看服务：chkconfig --list
   - chkconfig 服务名 --list
   - chkconfig --level 5 服务名 on/off

### systemctl指令

1. 基本语法
   - systemctl [start | stop | restart | status] 服务名
   - systemctl指令管理的服务在/usr/lib/systemd/system查看
2. systemctl设置服务的自启动状态
   - systemctl list-unit-files：查看服务开机启动状态
   - systemctl enable 服务名：设置服务开机启动
   - systemctl disable 服务名：关闭服务开启启动
   - systemctl is-enable 服务名：查询某个服务是否是自启动的

### firewall指令

打开端口：firewall-cmd --permanent --add-port=端口号/协议

关闭端口：firewall-cmd --permanent --remove-port=端口号/协议

重新载入才能生效：firewall-cmd --reload

查询端口是否开放：firewall-cmd --query-port=端口/协议

# 第五章 RPM与YUM

## 一，rpm包管理

1. 介绍

   rpm用于互联网下载包及安装工具，它包含在某些Linux分发版中，它生成具有.RPM扩展名的文件。RPM是RedHat Package Manager的缩写，类似于Windows的setup.exe，这一文件格式理念是通用的

2. rpm包名的简单查询指令

   rpm -qa：查询已安装的rpm列表

   rpm -q 软件包名：查询软件包是否安装

   rpm -qi 软件包名：查询软件包信息

   rpm -ql 软件包名：查询软件包中的文件

   rpm -qf 文件全路径名：查询文件所属的软件包

3. 卸载rpm包

   rpm -e rpm包名称：删除软件包

4. 安装rpm包

   rpm -ivh rpm包全路径名称：安装软件包

   - i：安装
   - v：提示
   - h：进度条

## 二，yum

1. 介绍

   yum是一个Shell前端软件包管理器，基于RPM包管理，能够从指定的服务器自动下载RPM包并且安装，可以自动处理依赖性关系，并且一次安装所有依赖的软件包

2. yum基本指令

   yum list：查询yum服务器是否有需要安装的软件

   yum install xxx：下载安装

# 第六章 搭建javaEE环境

## 一，安装jdk

1. mkdir /opt/jdk
2. 通过xftp6上传到/opt/jdk下
3. 解压 tar -zxvf jdk-8u321-linux-x64.tar.gz
4. mkdir /usr/local/java
5. mv /opt/jdk/jdk1.8.0_321 /usr/local/java
6. 配置环境变量vim /etc/profile
7. export JAVA_HOME=/usr/local/java/jdk1.8.0_321
8. export PATH=$JAVA_HOME/bin:$PATH
9. export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
10. source /etc/profile （让文件生效）

## 二，安装Tomcat

1. 上传安装文件，并解压缩到/opt/tomcat
2. 进入解压目录/bin，启动Tomcat，./startup.sh
3. 开放端口8080

## 三，安装MySQL

1. mkdir /opt/mysql：创建文件夹
2. wget https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-5.7.26-linux-glibc2.12-x86_64.tar.gz：下载
3. tar -zxvf mysql-5.7.26-linux-glibc2.12-x86_64.tar.gz：解压
4. systemctl start mysql.service：启动服务
5. grep "password" /var/log/mysqld.log：查看初始的随机密码
6. mysql -u root -p：登录
7. SET PASSWORD = PASSWORD("wgx")：修改密码
