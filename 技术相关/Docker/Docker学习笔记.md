# Docker 学习笔记

## 一、Docker  概述



## 二、Docker 基本组成

### 镜像（image）

Docker 镜像好比是一个模板，可以通过这个模板来创建容器，比如一个 Tomcat 镜像，可以用过 run 命令运行，创建一个或多个 Tomcat 容器。

### 容器（container）

Docker 利用容器技术，独立运行一个或一个组应用，通过镜像来创建的。

> 目前可以把容器理解为一个建议的 Linux 系统

### 仓库（repository）

仓库就是存放镜像的地方，类似于 github。

分为私有仓库和公有仓库：Docker Hub、阿里云等等

## run 命令流程和 Docker 原理

### run 命令流程

![run命令流程.png](D:\学习笔记\技术相关\Docker\Docker学习笔记.assets\run命令流程.png)

### Docker 底层原理

> Docker 是怎么工作的？

Docker 是一个 Client - Server 结构的系统，Docker 的守护进程运行在宿主机上，通过 Socket 从客户端访问；

Docker-Server 接收到 Docker-Client 的指令，就会执行这个指令（命令）。

![Docker是如何工作的](D:\学习笔记\技术相关\Docker\Docker学习笔记.assets\Docker是如何工作的.png)

### 为什么 Docker 比虚拟机快

1. Docker 有着比虚拟机更少的抽象层
2. Docker 利用的是宿主机的内核，虚拟机需要 Guest OS

下图是 Docker 与虚拟机的比较：

![为什么Dcoker比虚拟机快](D:\学习笔记\技术相关\Docker\Docker学习笔记.assets\为什么Dcoker比虚拟机快.png)

所以，新建一个容器的时候，Docker 不要像虚拟机那样重新加载一个操作系统内核，避免引导。

虚拟机是加载 Guest OS（分钟级别），而 Docker 利用宿主机的操作系统（秒级别）

## 三、Docker 的常用命令

### 帮助命令

#### docker version：显示版本信息

```shell
$ docker version
```

#### docker info：显示系统信息

> 包括镜像和容器的数量

```shell
$ docker info
```

#### docker 命令 --help：查看帮助文档

```shell
$ docker images --help
```

### 镜像命令

#### docker images：查看本地主机的所有镜像

```shell
$ docker images
REPOSITORY      TAG       IMAGE ID       CREATED        SIZE
hello-world     latest    feb5d9fea6a5   2 months ago   13.3kB
fatedier/frps   v0.36.2   d17cd3fc6a64   8 months ago   19.1MB

# 可选项
-a, --all       列出所有的镜像
-q, --quiet     只显示镜像的ID

# 查出所有镜像的ID
docker images -aq
```

#### docker search：搜索镜像

```shell
$ docker search mysql
NAME        DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
mysql       MySQL is a widely used, open-source relation…   11741     [OK]       
mariadb     MariaDB Server is a high performing open sou…   4476      [OK]
```

#### docker pull：下载镜像

```shell
# 下载最新版 docker pull 镜像名[:tag]
$ docker pull mysql
Using default tag: latest				# 如果不写 tag，默认使用最新版
latest: Pulling from library/mysql
a10c77af2613: Pull complete 			# 分层下载，docker image 核心（联合文件系统）
b76a7eb51ffd: Pull complete 
258223f927e4: Pull complete 
2d2c75386df9: Pull complete 
63e92e4046c9: Pull complete 
f5845c731544: Pull complete 
bd0401123a9b: Pull complete 
3ef07ec35f1a: Pull complete 
c93a31315089: Pull complete 
3349ed800d44: Pull complete 
6d01857ca4c1: Pull complete 
4cc13890eda8: Pull complete 
Digest: sha256:aeecae58035f3868bf4f00e5fc623630d8b438db9d05f4d8c6538deb14d4c31b  # 签名
Status: Downloaded newer image for mysql:latest   
docker.io/library/mysql:latest      # 真实地址

# 下载指定版本
$ docker pull mysql:5.7
5.7: Pulling from library/mysql
a10c77af2613: Already exists 		# 存在该文件，就不用下载，极大地节省了空间和时间
b76a7eb51ffd: Already exists 
258223f927e4: Already exists 
2d2c75386df9: Already exists 
63e92e4046c9: Already exists 
f5845c731544: Already exists 
bd0401123a9b: Already exists 
2724b2da64fd: Pull complete 
d10a7e9e325c: Pull complete 
1c5fd9c3683d: Pull complete 
2e35f83a12e9: Pull complete 
Digest: sha256:7a3a7b7a29e6fbff433c339fc52245435fa2c308586481f2f92ab1df239d6a29
Status: Downloaded newer image for mysql:5.7
docker.io/library/mysql:5.7
```

#### docker rmi：删除镜像

```shell
# 删除指定镜像
$ docker rmi -f 镜像ID

# 删除多个镜像
$ docker rmi -f 镜像ID 镜像ID 镜像ID

# 删除所有的镜像
$ docker rmi -f $(docker images -aq)
```

### 容器命令

> 说明：有了镜像才可以运行容器

下载一个 centos 镜像来测试学习：

```shell
$ docker pull centos
```

#### docker run：运行容器

```shell
# docker run [可选项] image

# 参数说明
--name="Name"			# 容器名称
-d							# 后台方式运行
-it							# 使用交互方式运行并进入容器
-p 							# 指定容器的端口  -p 8080:8080
  -p ip:主机端口:容器端口
  -p 主机端口：容器端口（常用）
  -p 容器端口
  容器端口
-P							# 大写P，随机指定端口

# 启动并进入容器
$ docker run -it centos /bin/bash
[root@b6dc6a0bbfff /]# 

# 从容器中退出到主机
[root@b6dc6a0bbfff /]# exit
```

#### docker ps：查看运行的容器

```shell
# docker ps [可选项]
-a		# 列出当前正在运行的容器，带出历史运行过的容器 
-n=?    # 列出最近创建的容器个数
-q      # 只显示容器ID

-aq     # 列出所有容器ID
```

#### docker rm：删除容器

```shell
$ docker rm 容器ID     			# 删除指定容器，但不能删除正在运行的容器，加 -f 强制删除
$ docker rm -f $(docker ps -aq)  # 删除所有容器
```

#### 容器启动和停止

```shell
$ docker start   容器ID    # 启动容器
$ docker restart 容器ID    # 重启容器
$ docker stop    容器ID    # 停止当前正在运行的容器
$ docker kill    容器ID    # 强制停止当前容器
```

### 其他命令

#### docker run -d：后台启动容器

```shell
# docker run -d 镜像名
$ docker run -d centos
4f58712a3b555bac04ac87e863d0ad3ff17a20c4f5845951cbf65da9a8fc2e7c
$ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```

出现问题：```docker ps``` 查看正在运行的容器时，发现 centos 停止了

解释：Docker 容器使用后台运行（-d 参数），就必须要有一个前台进程，Docker 发现没有应用，就会自动停止

> 2021年11月29日16:28:03：还是不太懂，留个坑

#### docker inspect：查看镜像的元数据

```shell
$ docker inspect 容器ID
```

#### docker exec -it：进入当前正在运行的容器（新的终端）

> 容器一般都是使用后台方式运行的，有时需要进入容器修改配置

```shell
# docker exec -it 容器ID bashShell
$ docker exec -it dce7b86171bf /bin/bash
```

#### docker attach：进入当前正在运行的容器（正在运行的终端）

```shell
# docker attach 容器ID
$ docker attach dce7b86171bf
```

#### docker cp：从容器拷贝文件到主机

> 拷贝是一个手动的过程，可以使用 -v 卷的技术，实现自动同步

```shell
# docker cp  容器ID:容器内路径 目标主机的路径
$ docker cp dce7b86171bf:/home/test.java /home部署 Nginx
```

### 可视化界面

#### portainer

```shell
$ docker run -d -p 8088:9000 \
  --restart=always -v /var/run/docker.sock:/var/run/docker.sock --privileged=true portainer/portainer
```

