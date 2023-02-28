# 第一章 Docker简介与安装

1. Docker是基于Go语言实现的云开源项目

   Docker的主要目标是“Build，Ship and Run Any App，Anywhere”，也就是通过对应用组件的封装、分发、部署、运行等生命周期 的管理，使用户的APP(可以是一个WEB应用或数据库应用等等)及其运行环境能够做到“一次镜像，处处运行”。

   Linux容器技术的出现就解决了这样一个问题，而Docker就是在它的基础上发展过来的。将应用打成镜像，通过镜像成为运行在 Docker容器上面的实例，而Docker容器在任何操作系统上都是一致的，这就实现了跨平台、跨服务器。只需要一次配置好环境， 换到别的机子上就可以一键部署好，大大简化了操作。

2. 安装

   官网(https://www.docker.com/)

   Docker并非是一个通用的容器工具，它依赖于已存在并运行的Linux内核环境。Docker实质上是在已经运行的Linux下制造了一个隔离的文件环境，因比它执行的效率几乎等同于所部署的Linux主机。 因此， Docker必须部署在Linux内核的系统上。如果其他系统想部署Docker就必须安装一个虚拟Linux环境

   - 卸载旧版本

     ```linux
     yum remove docker \
                       docker-client \
                       docker-client-latest \
                       docker-common \
                       docker-latest \
                       docker-latest-logrotate \
                       docker-logrotate \
                       docker-engine
     ```

   - 安装gcc相关

     ```linux
     yum -y install gcc
     yum -y install gcc-c++
     ```

   - 安装需要的软件包

     ```linux
     yum install -y yum-utils
     ```

   - 设置stable镜像仓库

     ```linux
     yum-config-manager \
         --add-repo \
         https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
     ```

   - 更新yum软件包索引

     ```linux
     yum makecache
     ```

   - 安装docker ce

     ```linux
     yum install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
     ```

   - 启动docker

     ```linux
     systemctl start docker
     ```

   - 查看docker版本

     ```linux
     docker version
     ```

   - hello-world

     ```linux
     docker run hello-world
     ```

3. Docker的基本组成

   - 镜像（image）

     Docker镜像(Image)就是一个只读的模板。镜像可以用来创建Docker容器， 一个镜像可以创建很多容器。 它也相当于是一个root文件系统。比如官方镜像centos7就包含了完整的一套centos7最小系统的root文件系统。 相当于容器的“源代码”，docker镜像文件类似于Java的类模板，而docker容器实例类似于java中new出来的实例对象。

   - 容器（container）

     Docker利用容器(Container)独立运行的一个或一组应用，应用程序或服务运行在容器里面，容器就类似于一个虚拟化的运行环 境，容器是用镜像创建的运行实例。就像是java中的类和实例对象一样，镜像是静态的定义，容器是镜像运行时的实体。容器为镜 像提供了一个标准的和隔离的运行环境，它可以被启动、开始、停止、删除。每个容器都是相互隔离的、保证安全的平台。

   - 仓库（repository）

     仓库分为公开仓库(Public)和私有仓库(Private)两种形式。 最大的公开仓库是Docker Hub(https://hub.docker.com)，存放了数量庞大的镜像供用户下载。国内的公开仓库包括阿里云、网易云等

4. 配置阿里云镜像加速器

   - 修改daemon配置文件

     ```linux
     sudo mkdir -p /etc/docker
     sudo tee /etc/docker/daemon.json <<-'EOF'
     {
       "registry-mirrors": ["https://3qc1e587.mirror.aliyuncs.com"]
     }
     EOF
     sudo systemctl daemon-reload
     sudo systemctl restart docker
     ```

# 第二章 Docker常用命令

## 一，帮助启动类命令

1. 启动docker：systemctl start docker
2. 停I止docker：systemctl stop docker
3. 重启docker：systemctl restart docker
4. 查看docker状态：systemctl status docker
5. 开机启动：systemctl enable docker
6. 查看docker概要信息：docker info
7. 查看docker总体帮助文档：docker --help
8. 查看docker命令帮助文档：docker 具体命令 --help

## 二，镜像命令

1. docker images：列出本地主机上的镜像

   - -a：列出本地所有的镜像（含历史）
   - -q：只显示镜像ID
   - ![](F:\截图\屏幕截图 2023-02-16 150530.png)

2. docker search xxx：搜索某个镜像

   - --limit N：只列出N个镜像，默认25个
   - ![](F:\截图\屏幕截图 2023-02-16 150847.png)

3. docker pull xxx：下载某个镜像

   - docker pull xxx:TAG

4. docker system df：查看镜像/容器/数据卷所占的空间

   ![](F:\截图\屏幕截图 2023-02-16 151541.png)

5. docker rmi 镜像ID：删除某个镜像

   - -f：强制删除
   - 删除多个：docker rmi 镜像名1:TAG 镜像名2:TAG
   - 删除全部：docker rmi -f $(docker images -qa)

6. 仓库名、标签都是<none\>的镜像，俗称虚悬镜像dangling image

## 三，容器命令

1. docker run [OPTIONS] IMAGE [COMMAND] [ARG...]

   - --name="容器新名字"：为容器指定一个名称
   - -d：后台运行容器并返回容器ID，即为启动守护式容器（后台运行）
   - -i：以交互模式运行容器，通常与-t同时使用
   - -t：为容器重新分配一个伪输入终端，通常与-i同时使用
   - -P：随机端口映射
   - -p：指定端口映射

2. docker ps [OPTIONS]

   - -a：列出当前所有正在运行的容器（包含历史上运行过的）
   - -l：显示最近创建的容器
   - -n：显示最近n个创建的容器
   - -q：静默模式，只显示容器编号

3. 退出容器

   - exit：退出并停止
   - Ctrl+p+q：退出不停止

4. 启动已停止运行的容器

   docker start 容器ID或者容器名

5. 重启容器

   docker restart 容器ID或者容器名

6. 停止容器

   docker stop 容器ID或者容器名

7. 强制停止容器

   docker kill 容器ID或者容器名

8. 删除已停止的容器

   - docker rm [-f] 容器ID或者容器名
   - docker rm -f $(docker ps -aq)

9. 查看容器内运行的进程

   docker logs 容器ID

10. 查看容器内部细节

    docker inspect 容器ID

11. 进入正在运行的容器并以命令行交互

    - docker exec -it 容器ID bashShell

      exec是在容器中打开新的终端，并且可以启动新的进程用exit退出，不会导致容器的停止。

    - docker attach 容器ID

      attach直接进入容器启动命令的终端，不会启动新的进程用exit退出，会导致容器的停止。

12. 从容器内拷贝文件到主机上

    docker cp 容器ID:容器内路径 目的主机路径

13. 导入和导出容器

    - 导出容器的内容留作一个tar归档文件

      docker export 容器ID > 文件名.tar

    - 从tar包中的内容创建一个新的文件系统再导入为镜像

      cat 文件名.tar | docker import - 镜像用户/镜像名:镜像版本号

14. 提交容器

    docker commit -m="提交描述信息" -a="作者" 容器ID 要创建的目标镜像名:[TAG]

15. 推送到阿里云仓库

    - 登录到阿里云Docker Registry

      ```linux
      docker login --username=wgx2297665453 registry.cn-shanghai.aliyuncs.com
      ```

    - 推送

      ```linux
      docker tag [ImageId] registry.cn-shanghai.aliyuncs.com/wugongxun/wgx:[镜像版本号]
      
      docker push registry.cn-shanghai.aliyuncs.com/wugongxun/wgx:[镜像版本号]
      ```

    - 拉取

      ```linux
      docker pull registry.cn-shanghai.aliyuncs.com/wugongxun/wgx:[镜像版本号]
      ```

# 第三章 Docker容器数据卷

1. 卷就是目录或文件，存在于一个或多个容器中，由docker挂载到容器，但不属于联合文件系统，因此能够绕过Union File System提供 一些用于持续存储或共享数据的特性

   卷的设计目的就是数据的持久化，完全独立于容器的生存周期， 因此Docker不会在容器删除时删除其挂载的数据卷

2. 运行一个带有容器卷存储功能的容器实例

   ```linux
   docker run -it --privileged=true -v /宿主机的绝对路径目录:/容器内目录 镜像名
   ```

3. 特点

   - 数据卷可在容器之间共享或重用数据
   - 卷中的更改可以直接实时生效
   - 数据卷中的更改不会包含在镜像的更新中
   - 数据卷的生命周期一直持续到没有容器使用它为止

4. 查看数据卷是否挂载成功

   ```linux
   docker inspect 容器ID
   ```

5. 设置只读或者读写规则

   ```linux
   docker run -it --privileged=true -v /宿主机的绝对路径目录:/容器内目录:[ro|rw] 镜像名
   ```

   - ro（只读）：宿主机可以写入内容，同步给容器内，容器可以读取到。容器不能写入内容

6. 容器的继承

   ```linux
   docker run -it --privileged=true --volumes-from 父镜像 子镜像
   ```

7. docker启动mysql并绑定数据卷

   ```linux
   docker run -d -p 3306:3306 --privileged=true -v /home/wgx/mysql/log:/var/log/mysql -v /home/wgx/mysql/data:/var/lib/mysql -v /home/wgx/mysql/conf:/etc/mysql/conf.d -e MYSQL_ROOT_PASSWORD=wgx --name=mysql mysql:5.7
   ```

8. docker启动redis并绑定数据卷

   ```linux
   docker run -d -p 6379:6379 --privileged=true -v /home/wgx/redis/redis.conf:/etc/redis/redis.conf -v /home/wgx/redis/data:/data --name=redis redis redis-server /etc/redis/redis.conf
   ```


# 第四章 集群搭建

## 一，mysql

- 启动主机并绑定数据卷

  ```linux
  docker run -d -p 3307:3306 --privileged=true -v /home/wgx/mysql-master/data:/var/lib/mysql -v /home/wgx/mysql-master/log:/var/log/mysql -v /home/wgx/mysql-master/conf:/etc/mysql/conf.d -e MYSQL_ROOT_PASSWORD=wgx --name mysql-master mysql:5.7
  ```

- 修改主机的my.cnf

  ```mysql
  [mysqld]
  ##设置server_id,同一局域网中需要唯一 
  server_id=101
  ##指定不需要同步的数据库名称
  binlog-ignore-db=mysql
  #开启二进制日志功能
  log-bin=mall-mysql-bin
  ##设置二进制日志使用内存大小（事务）
  binlog_cache_size=1M
  ##设置使用的二进制日志格式(mixed,statement,row)
  binlog_format-mixed
  ##二进制日志过期清理时间。默认值为0：表示不自动清理。
  expire_logs_days=7
  ##跳过主从复制中遇到的所有错误或指定类型的错误，避免slave端复制中断。 
  ##如：1062错误是指一些主键重复，1032错误是因为主从数据库数据不一致
  slave_skip_errors=1062
  ```

- 进入主机，创建数据同步用户

  ```mysql
  REATE USER 'slave'@'%' IDENTIFIED BY 'wgx';
  
  GRANT REPLICATION SLAVE, REPLICATION CLIENT ON *.* TO 'slave'@'%';
  ```

- 启动从机并绑定数据卷

  ```linux
  docker run -d -p 3308:3306 --privileged=true -v /home/wgx/mysql-slave/data:/var/lib/mysql -v /home/wgx/mysql-slave/log:/var/log/mysql -v /home/wgx/mysql-slave/conf:/etc/mysql/conf.d -e MYSQL_ROOT_PASSWORD=wgx --name mysql-slave mysql:5.7
  ```

- 修改从机的my.cnf

  ```mysql
  [mysqld]
  ##设置server_id,同一局域网中需要唯一 
  server_id=102
  ##指定不需要同步的数据库名称
  binlog-ignore-db=mysql
  #开启二进制日志功能，以备Slave作为其他数据库实例的Master时使用
  log-bin=mall-mysql-slave1-bin
  ##设置二进制日志使用内存大小（事务）
  binlog_cache_size=1M
  ##设置使用的二进制日志格式(mixed,statement,row)
  binlog_format=mixed
  ##二进制日志过期清理时间。默认值为0：表示不自动清理。
  expire_logs_days=7
  ##跳过主从复制中遇到的所有错误或指定类型的错误，避免slave端复制中断。 
  ##如：1062错误是指一些主键重复，1032错误是因为主从数据库数据不一致
  slave_skip_errors=1062
  ##relay_log配置中继日志
  relay_log=mall-mysql-relay-bin
  ##log_slave_updates表示slave将复制事件写进自己的二进制日志
  log_slave_updates=1
  ##slave设置为只读（具有super权限的用户除外）
  read_only=1
  ```

- 进入从机配置主从复制

  ```mysql
  change master to master_host='主机ip', mas
  ter_user='slave', master_password='wgx', master_port=3307, master_log_file='mall-mysql-bin.000002', master_log_pos=154, master_connect_retry=30;
  ```

- 在从数据库中开启主从同步

  ```mysql
  start slave;
  ```

## 二，redis

1. 分布式存储

   - 哈希取余算法

     用户每次读写操作都是根据公式：hash(key) % N个机器台数，计算出hash值来决定映射到哪一个节点上

   - 一致性哈希算法

     - 一致性哈希环

       一致性哈希算法必然有个hash函数并按照算法产生hash值，这个算法的所有可能哈希值会构成一个全量集，这个集合可以成为一个hash空间 [0, 2^32^-1]，这个是一个线性空间，但是在算法中，我们通过适当的逻辑控制将它首尾相连(0=2^32^)，这样让它逻辑上形成了一个环形空间。

       他也是按照使用取模的方法，但是一致性哈希算法是对2^32^取模。简单来说，一致性哈希算法将整个哈希值组织成一个虚拟的圆环。

     - 服务器ip节点映射

       将集群中各个ip节点映射到环上的某一个位置。 

       将各个服务器使用Hash进行一个哈希，具体可以选择服务器的ip或主机名作为关键字进行哈希，这样每台机器就能确定其在哈希坏上的位置。假如 4个节点NodeA、B、C、D，经过IP地址的哈希函数计算(hash(ip))，使用IP地址哈希后在环空间的位置如下：

       ![](F:\截图\屏幕截图 2023-02-19 164259.png)

     - key落到服务器的落键规则

       当我们需要存储一个kv键值对时，首先计算key的hash值hash(key)，将这个key使用相同的函数Hash计算出哈希值并确定此数据在环上的位置，从此位置沿环顺时针“行走”，第一台遇到的服务器就是其应该定位到的服务器，并将该键值对存储在该节点上。

        如我们有Object A、Object B、Object C、Object D四个数据对象，经过哈希计算后，在环空间上的位置如下：

       ![](F:\截图\屏幕截图 2023-02-19 164608.png)

       根据一致性Hash算法，数据A会被定 为到Node A上，B被定为到Node B上，C被定为到Node C上，D被定为到Node D上。

   - 哈希槽分区

     哈希槽实质就是一个数组，数组[0, 2^14^-1]形成hash slot空间

     解决均匀分配的问题，在数据和节点之间又加入了一层，把这层称之为哈希槽（slot），用于管理数据和节点之间的关系，现在就相当于节点上放的是槽，槽里放的是数据

     ![](F:\截图\屏幕截图 2023-02-19 170220.png)

     槽解决的是粒度问题，相当于把粒度变大了，这样便于数据移动

     哈希解决的是映射问题，使用key的哈希值来计算所在的槽，便于数据分配

     一个集群只能有16384个槽，编号0-16383(0~2^14^-1)。这些槽会分配给集群中的所有主节点，分配策略没有要求。可以指定哪些编号的槽分配给哪个主节点。集群会记录节点和槽的对应关系。解决了节点和槽的关系后，接下来就需要对key求哈希值，然后对16384取余，余数是几key就落入对 应的槽里。slot=CRC16(key)%16384。以槽为单位移动数据，因为槽的数目是固定的，处理起来比较容易，这样数据移动问题就解决了。

2. 集群搭建

   - 启动所有的redis节点

     ```linux
     docker run -d --privileged=true -v /home/wgx/redis-nodes/redis-node-1:/data --net host --name=redis-node-1 redis:latest --cluster-enabled yes --appendonly yes --port 6379
     
     docker run -d --privileged=true -v /home/wgx/redis-nodes/redis-node-2:/data --net host --name=redis-node-2 redis:latest --cluster-enabled yes --appendonly yes --port 6380
     
     docker run -d --privileged=true -v /home/wgx/redis-nodes/redis-node-3:/data --net host --name=redis-node-3 redis:latest --cluster-enabled yes --appendonly yes --port 6381
     
     docker run -d --privileged=true -v /home/wgx/redis-nodes/redis-node-4:/data --net host --name=redis-node-4 redis:latest --cluster-enabled yes --appendonly yes --port 6382
     
     docker run -d --privileged=true -v /home/wgx/redis-nodes/redis-node-5:/data --net host --name=redis-node-5 redis:latest --cluster-enabled yes --appendonly yes --port 6383
     
     docker run -d --privileged=true -v /home/wgx/redis-nodes/redis-node-6:/data --net host --name=redis-node-6 redis:latest --cluster-enabled yes --appendonly yes --port 6384
     ```

   - 进入任意一台主机，执行

     ```linux
     redis-cli --cluster create 127.0.0.1:6379 127.0.0.1:6380 127.0.0.1:6381 127.0.0.1:6382 127.0.0.1:6383 127.0.0.1:6384 --cluster-replicas 1
     ```

     ![](F:\截图\屏幕截图 2023-02-20 152208.png)

   - 查看集群状态

     ```linux
     cluster info
     cluster nodes
     ```

   - 使用集群方式连接

     ```linux
     redis-cli -c
     ```

   - 查看集群信息

     ```linux
     redis-cli --cluster check 127.0.0.1:6379
     ```

     ![](F:\截图\屏幕截图 2023-02-20 154148.png)

   - 添加新的集群节点(127.0.0.1:6385)

     ```linux
     redis-cli --cluster add-node 127.0.0.1:6385 127.0.0.1:6379
     ```

     ![](F:\截图\屏幕截图 2023-02-20 160440.png)

   - 重新分配槽号

     ```linux
     redis-cli --cluster reshard 127.0.0.1:6379
     ```

     ![](F:\截图\屏幕截图 2023-02-20 161409.png)

     - 需要重新分配多少槽位（16384 / 4 = 4096）

     - 接受的节点ID

     - 所有的节点重新分配（all）

     - 重新分配后的槽位变化

       ![](F:\截图\屏幕截图 2023-02-20 161700.png)

   - 为新的master节点(127.0.0.1:6385)添加slave节点(127.0.0.1:6386)

     ```linux
     redis-cli --cluster add-node 127.0.0.1:6386 127.0.0.1:6385 --cluster-slave --cluster-master-id 4721fc1310306162fd7dd095cedf285bf2ce540f
     ```

   - 删除集群节点

     - 先删除从机(127.0.0.1:6386)

       ```linux
       redis-cli --cluster del-node 127.0.0.1:6386 从机ID
       ```

     - 重新分配槽号

       ```linux
       redis-cli --cluster reshard 127.0.0.1:6379
       ```

       ![](F:\截图\屏幕截图 2023-02-20 170608.png)

       - 需要重新分配多少槽位（ 4096）
       - 接受的节点ID
       - 指定节点贡献槽位

     - 删除主机(127.0.0.1:6385)

       ```linux
       redis-cli --cluster del-node 127.0.0.1:6385 主机ID
       ```


# 第五章 DockerFile

## 一，DockerFile构建过程解析

1. DockerFile基础内容
   - 每条保留字指令都必须为大写且后面要跟随至少一个参数
   - 指令按照从上到下，顺序执行
   - #表示注释
   - 每条指令都会创建一个新的镜像层并对镜像进行提交
2. Docker执行DockerFile大致流程
   - docker从基础镜像运行一个容器
   - 执行一条指令并对容器作出修改
   - 执行类似docker commit的操作提交一个新的镜像层
   - docker再基于刚提交的镜像运行一个新容器
   - 执行dockerfile中的下一条指令直到所有指令都执行完成

## 二，DockerFile常用保留字

1. FROM

   基础镜像，当前新镜像是基于哪个镜像的，指定一个已经存在的镜像作为模板，第一条必须是FROM

2. MAINTAINER

   镜像维护者的姓名和邮箱地址

3. RUN

   - 容器构建时需要运行
   - 两种格式
     - shell格式：RUN <命令>
     - exec格式：RUN ["可执行文件", "参数1", "参数2"...]
   - RUN是在docker build时运行

4. EXPOSE

   当前容器对外暴露的端口

5. WORKDIR

   指定在创建容器后，终端默认登陆进来的工作目录

6. USER

   指定该镜像以什么样的用户去执行，如果都不指定，默认是root

7. ENV

   用来在构建镜像过程中设置环境变量

8. VOLUME

   容器数据卷，用于数据保存和持久化工作

9. ADD

   将宿主机目录下的文件拷贝进镜像且会自动处理URL和解压tar压缩包

10. COPY

    类似ADD，拷贝文件和目录到镜像中。COPY <src\> <dest\>

11. CMD

    - 指定容器启动后要做的事情
      - shell格式：CMD <命令>
      - exec格式：CMD ["可执行文件", "参数1", "参数2"...]
      - 参数列表格式：CMD ["参数1", "参数2"...]，在指定了ENTRYPOINT指令后，用CMD指定具体的参数

    - DockerFile中可以有多个CMD指令，但只有最后一个生效，CMD会被docker run 之后的参数替换掉
    - CMD是在docker run时运行

12. ENTRYPOINT

    类似于CMD指令，但是ENTRYPOINT不会被docker run后面的命令覆盖，而且这些命令行参数会被当做参数送给ENTRYPOINT指令指定的程序

## 三，DockerFile案例

安装自带vim、ifconfig和jdk8的centos

1. 编写Dockerfile文件

   ```dockerfile
   FROM centos:7
   MAINTAINER wgx<2297665453@qq.com>
   
   ENV MYPATH /usr/local
   WORKDIR $MYPATH
   
   #安装vim编辑器
   RUN yum -y install vim
   #安装ifconfig命令查看网络IP
   RUN yum -y install net-tools
   #安装jdk8以及lib库
   RUN yum -y install glibc.i686
   RUN mkdir /usr/local/java
   #ADD是相对路径，把jdk-8u361-linux-x64.tar.gz添加到容器中，安装包必须和Dockerfile文件在同一位置
   ADD jdk-8u361-linux-x64.tar.gz /usr/local/java/
   #配置java环境变量
   ENV JAVA_HOME /usr/local/java/jdk1.8.0_361
   ENV JRE_HOME $JAVA_HOME/jre
   ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib:$CLASSPATH
   ENV PATH $JAVA_HOME/bin:$PATH
   
   EXPOSE 80
   
   CMD echo $MYPATH
   CMD echo "------------------------success-----------------------"
   CMD /bin/bash
   ```

2. 构建

   docker build -t 镜像名字:TAG .

## 四，虚悬镜像

1. 仓库名、标签都是<none\>的镜像叫虚悬镜像

2. 查看所有的虚悬镜像

   ```linux
   docker images ls -f dangling=true
   ```

3. 删除虚悬镜像

   ```linux
   docker images prune
   ```

# 第六章 Docker发布微服务

1. 将微服务打成jar包

   ![](F:\截图\屏幕截图 2023-02-22 152204.png)

2. 上传到服务器

   ![](F:\截图\屏幕截图 2023-02-22 152423.png)

3. 编写Dockerfile

   ```dockerfile
   FROM openjdk:17
   MAINTAINER wgx<2297665453@qq.com>
   #VOLUME指定临时文件目录为/tmp，在主机/var/lib/docker目录下创建一个临时文件并链接到容器的/tmp
   VOLUME /tmp
   #将jar包添加到容器中并更名为docker.jar
   ADD docker-1.0-SNAPSHOT.jar docker.jar
   #运行jar包
   RUN bash -c 'touch /docker.jar'
   ENTRYPOINT ["java", "-jar", "/docker.jar"]
   
   #暴露8081端口作为微服务
   EXPOSE 8081
   ```

4. 构建

   ```linux
   docker build -t docker_server:1 .
   ```

# 第七章 Docker网络

## 一，Docker网络的常用命令

1. 查看网络

   ```linux
   docker network ls
   ```

   ![](F:\截图\屏幕截图 2023-02-22 170650.png)

2. 查看网络数据源

   ```linux
   docker network inspect xxx
   ```

   ![](F:\截图\屏幕截图 2023-02-22 170938.png)

3. 删除网络

   ```linux
   docker network rm xxx
   ```

## 二，Docker网络的作用

1. 容器间的互联和通信以及端口映射
2. 容器IP变动时候可以通过服务名直接网络通信而不受影响

## 三，Docker的网络模式

| 网络模式  | 简介                                                         | 使用                               |
| --------- | ------------------------------------------------------------ | ---------------------------------- |
| bridge    | 为每一个容器分配、设置IP等，并将容器连接到一个docker0，==虚拟网桥，默认为该模式== | --network bridge，默认使用docker0  |
| host      | 容器将不会虚拟出自己的网卡，配置自己的IP等，而是使用宿主机的IP和端口 | --network host                     |
| none      | 容器有独立的Network namespace，但并没有对其进行任何网络设置，如分配veth pair和网桥连接，IP等 | --network none                     |
| container | 新创建的容器不会创建自己的网卡和配置自己的IP，而是和一个指定的容器共享IP、端口范围等 | --network container:NAME或者容器ID |

## 四，bridge

1. Docker使用Linux桥接，在宿主机虚拟一个Docker容器网桥(docker0)，Docker启动一个容器时会根据Docker网桥的网段分配给容器一个IP地址，称为Container-lP，同时Docker网桥是每个容器的默认网关。因为在同一宿主机内的容器都接入同一个网桥，这样容器之间就能够通过容器的 Container-IP直接通信。
2. docker run的时候，没有指定network的话默认使用的网桥模式就是bridge,使用的就是docker0。在宿主机ifconfig，就可以看到docker0和自己 create的network。eth0，eth1，eth2…代表网卡一，网卡二，网卡三...，lo代表127.0.0.1，即localhost，inet addr用来表示网卡的IP地址
3. 网桥docker0创建一对对等虚拟设备接口一个叫veth，另一个叫eth0，成对匹配。 
   - 整个宿主机的网桥模式都是docker0，类似一个交换机有一堆接口，每个接口叫veth，在本地主机和容器内分别创建一个虚拟接口，并让他们彼此联通（这样一对接口叫veth pair）
   - 每个容器实例内部也有一块网卡，每个接口叫eth0
   - docker0上面的每个veth匹配某个容器实例内部的eth0，两两配对，一一匹配。
   - 通过上述，将宿主机上的所有容器都连接到这个内部网络上，两个容器在同一个网络下，会从这个网关下各自拿到分配的，此时两个容器的网络是互通的。

![](F:\截图\屏幕截图 2023-02-22 173919.png)

## 五，host

1. 容器将不会获得一个独立的Network Namespace，而是和宿主机共用一个Network Namespace。容器将不会虚拟出自己的网卡而是使用宿主机的网络IP和端口
2. docker启动时指定--network=host或者--net=host，如果还指定了-p映射端口，那么这个时候就会有警告，并且通过-p设置的参数将不会起到任何作用，端口会以主机端口为主，重复时递增

## 六，none

在none模式下，并不为Docker容器进行任何网络配置。也就是说，这个Docker容器没有网卡、IP、路由等信息，只有一个lo，需要我们自己为Docker容器添加网卡，配置IP等

## 七，container

新创建的容器不会创建自己的网卡和配置自己的IP，而是和一个指定的容器共享IP、端口范围等

## 八，自定义网络

1. 新建自定义网络

   ```linux
   docker network create my_network
   ```

   ![](F:\截图\屏幕截图 2023-02-24 142828.png)

2. 新建容器加入上一步新建的自定义网络

   ```linux
   docker run -d -p 8081:8080 --network my_network --name=tomcat81 tomcat
   
   docker run -d -p 8082:8080 --network my_network --name=tomcat82 tomcat
   ```

   自定义网络本身就维护好了主机名和ip的对应关系

# 第八章 Docker容器编排

## 一，Docker-Compose简介与安装

1. Compose是Docker公司推出的一个工具软件，可以管理多个Docker容器组成一个应用。你需要定义一个YAML格式的配置文件docker-compose.yml,写好多个容器之间的调用关系。然后，只要一个命令，就能同时启动/关闭这些容器。

2. 下载安装

   ```linux
   curl -SL https://github.com/docker/compose/releases/download/v2.16.0/docker-compose-linux-x86_64 -o /usr/local/bin/docker-compose
   ```

## 二，Compose常用命令

1. docker compose -h #查看帮助
2. docker compose up #启动所有docker-compose服务
3. docker compose up -d #启动所有docker--compose服务并后台运行
4. docker compose down #停止并删除容器、网络、卷、镜像。
5. docker compose exec yml里面的服务id #进入容器实例内部
6. docker compose exec docker-compose.yml文件中写的服务id bash
7. docker compose ps #展示当前docker-compose:编排过的运行的所有容器
8. docker compose top #展示当前docker-compose编排过的容器进程
9. docker compose logs yml里面的服务id #查看容器输出日志
10. docker compose config #检查配置
11. docker compose config -q #检查配置，有问题才有输出
12. docker compose restart #重启服务
13. docker compose start #启动服务
14. docker compose stop #停止服务

## 三，Compose编排微服务

1. 编写docker-compose.yml文件

   ```yaml
   version: "3"
   
   services: 
     microService:
       image: docker_server:4
       container_name: docker_server
       ports: 
         - "8081:8081"
       volumes: 
         - /tmp:/data
       networks: 
         - my_network
       depends_on: 
         - redis
         - mysql
   
     redis: 
       image: redis
       ports: 
         - "6379:6379"
       volumes: 
         - /home/wgx/redis/data:/data
         - /home/wgx/redis/redis.conf:/etc/redis/redis.conf
       networks: 
         - my_network
       command: redis-server /etc/redis/redis.conf
     
     mysql: 
       image: mysql:5.7
       environment: 
         MYSQL_ROOT_PASSWORD: 'wgx'
         MYSQL_ALLOW_EMPTY_PASSWORD: 'no'
         MYSQL_DATABASE: 'db01'
         MYSQL_USER: 'wgx'
         MYSQL_PASSWORD: 'wgx'
       ports: 
         - "3306:3306"
       volumes: 
         - /home/wgx/mysql/data:/var/lib/mysql
         - /home/wgx/mysql/conf/my.cnf:/etc/my.cnf
         - /home/wgx/mysql/:/docker-entrypoint-initdb.d
       networks: 
         - my_network
       command: --default-authentication-plugin=mysql_native_password #解决外部无法访问
   
   networks: 
     my_network: 
   ```

2. 将微服务中的ip地址换成服务名

   ![](F:\截图\屏幕截图 2023-02-27 154530.png)

3. 将微服务构建docker镜像

   执行docker compose config -q查看yml文件是否有错误

4. 启动所有服务

   docker compose up -d

# 第九章 可视化

## 一，Portainer

1. 安装并运行

   ```linux
   docker run -d -p 8000:8000 -p 9000:9000 --restart=always --privileged=true -v /var/run/docker.sock:/var/run/docker.sock portainer/portainer
   ```

2. 访问地址

   http://192.168.154.128:9000/

   ![](F:\截图\屏幕截图 2023-02-27 163017.png)

## 二，CIG

1. 安装与运行

   - docker-compose.yml

     ```yaml
     version: '3.1'
     volumes:
       grafana_data: {}
     
     services:
       influxdb:
         image: tutum/influxdb:0.9
         restart: always
         environment: 
           - PRE_CREATE_DB=cadvisor
         ports:
           - "8083:8083"
           - "8086:8086"
         volumes:
           - ./data/influxdb:/data
                  
       cadvisor:
         image: google/cadvisor
         links:
           - influxdb:influxsrv
         command: -storage_driver=influxdb -storage_driver_db=cadvisor -storage_driver_host=influxsrv:8086
         restart: always
         ports:
           - "8080:8080"
         volumes:
           - /:/rootfs:ro
           - /var/run:/var/run:rw
           - /sys:/sys:ro
           - /var/lib/docker/:/var/lib/docker:ro
     
       grafana:
         user: "104"
         image: grafana/grafana
         user: "104"
         restart: always
         links:
           - influxdb:influxsrv
         ports:
           - "3000:3000"
         volumes:
           - grafana_data:/var/lib/grafana
         environment:
           - HTTP_USER=admin
           - HTTP_PASS=admin
           - INFLUXDB_HOST=influxsrv
           - INFLUXDB_PORT=8086
           - INFLUXDB_NAME=cadvisor
           - INFLUXDB_USER=root
           - INFLUXDB_PASS=root
     ```

   - 执行docker compose up

2. 测试

   - CAdvisor收集服务：http://ip:8080/
   - influxdb存储服务：http://ip:8083/
   - grafana展现服务：http://ip:3000/

