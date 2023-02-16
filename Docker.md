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

15. 

