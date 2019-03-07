# 一、Docker 介绍

## 什么是 Docker?

　　Docker 使用 [Go 语言](https://golang.org/) 进行开发实现，基于 Linux 内核的 [cgroup](https://zh.wikipedia.org/wiki/Cgroups)，[namespace](https://en.wikipedia.org/wiki/Linux_namespaces)，以及 [AUFS](https://en.wikipedia.org/wiki/Aufs) 类的 [Union FS](https://en.wikipedia.org/wiki/Union_mount) 等技术，对进程进行封装隔离，属于 [操作系统层面的虚拟化技术](https://en.wikipedia.org/wiki/Operating-system-level_virtualization)。由于隔离的进程独立于宿主和其它的隔离的进程，因此也称其为容器。

　　Docker 在 Linux 容器的基础上，进行了进一步的封装，从文件系统、网络互联到进程隔离等等，极大的简化了容器的创建和维护。使得 Docker 技术比虚拟机技术更为轻便、快捷。

## 为什么要使用 Docker

　　容器不需要进行硬件虚拟以及运行完整操作系统等额外开销。传统虚拟化方式运行 10 个不同的应用就要起 10 个虚拟机，而Docker 只需要启动 10 个隔离容器应用即可。

Docker 相比传统的虚拟化方式具有以下的优势

**1、更快速的启动时间**

　　传统的虚拟机技术启动应用服务往往需要数分钟，而 Docker 容器应用，由于直接运行于宿主内核，无需启动完整的操作系统，因此可以做到秒级、甚至毫秒级的启动时间。大大的节约了开发、测试、部署的时间。

**2、一致的运行环境**

　　Docker 的镜像提供了除内核外完整的运行时环境，确保了应用运行环境一致性。

**3、持续交付和部署**

　　使用 Docker 可以通过定制应用镜像来实现持续集成、持续交付、部署。

**4、更轻松的迁移**

　　Docker 容器可以在很多平台上运行，无论是物理机、虚拟机、公有云、私有云，其运行结果是一致的。

**Docker vs VM**

　　传统虚拟机技术是虚拟出一套硬件后，在其上运行一个完整操作系统，在该系统上再运行所需应用进程；

　　容器内的应用进程直接运行于宿主的内核，容器内没有自己的内核，而且也没有进行硬件虚拟。因此容器要比传统虚拟机更为轻便。
　　![img](https://github.com/monsterhxw/docker-learning/blob/master/images/vmandcontainer.jpg)

## 二、Docker 的组成
### Docker 是C/S架构：
![img](https://github.com/monsterhxw/docker-learning/blob/master/images/dockerEngine.png)

- CLient docker CLI : 命令行界面工具的客户端。
- REST API : 客户端与守护进程进行通信的接口。
- Server docker daemon : 守护进程，运行在宿主机上。
- Image : 镜像是用于创建容器的模板。
- Container : 容器是独立运行的一个或一组应用。
- Registry : 用来保存镜像的仓库，官网的仓库是Docker Hub。

### Docker 镜像

　　Docker 镜像是一个特殊的文件系统，除了提供容器运行时所需的程序、库、资源、配置等文件外，还包含了一些为运行时准备的一些配置参数（如匿名卷、环境变量、用户等）。镜像不包含任何动态数据，其内容在构建之后也不会被改变。

　　镜像采用分层存储的架构，由多层文件系统联合组成，镜像构建时，会一层层构建，前一层是后一层的基础。每一层构建完就不会再发生改变，后一层上的任何改变只发生在自己这一层。

### Docker 容器

　　镜像（Image）和容器（Container）的关系，就像是面向对象程序设计中的类和对象一样，镜像是静态的定义，容器是镜像运行时的实体。容器可以被创建、启动、停止、删除、暂停等。

　　容器的实质是进程，运行在一个隔离的环境里。

　　容器消亡时，任何保存于容器存储层的信息都会随容器删除而丢失。

### 数据卷

　　因为容器消亡时，存于容器存储层的信息也会随之删除，所以所有的文件写入操作，都应该使用 数据卷（Volume）、或者绑定宿主目录，在这些位置的读写会跳过容器存储层，直接对宿主（或网络存储）发生读写。

　　数据卷的生存周期独立于容器，容器消亡，数据卷不会消亡。

**数据卷特性**

- 可以在容器之间共享和重用
- 对数据卷的修改会立马生效
- 对数据卷的更新不会影响镜像
- 数据卷默认会一直存在


## 三、安装 Docker (在 CentOS 7 上安装) 

1、 使用 yum 命令进行安装Docker CE

```shell
[root@localhost ～]# yum install docker
```

2、 启动 Docker 服务，并将其设置为开机启动

```shell
[root@localhost ～]# systemctl start docker.service
[root@localhost ～]# systemctl enable docker.service
```

3、 验证安装，查看 Docker 版本信息

```shell
[root@localhost ～]# docker version
```

4、 国内连接 Docker 的官方仓库很慢，因此我们在日常使用中会使用Docker 中国加速器。通过 Docker 官方镜像加速

```shell
[root@localhost ～]# vi  /etc/docker/daemon.json

	{
	    "registry-mirrors": ["https://registry.docker-cn.com"],
	    "live-restore": true
	}
	
```

5、重启服务

```shell
[root@localhost ～]# systemctl daemon-reload
[root@localhost ～]# systemctl restart docker
```

## Docker 常用命令

![docker-cmd](https://github.com/monsterhxw/docker-learning/blob/master/images/docker-cmd.png)

## Docker 镜像命令
#### 1. 拉取镜像其命令格式为：
```shell
[root@localhost ~]# docker pull [选项] [Docker Registry 地址[:端口号]/]仓库名[:标签]
```
![img](https://github.com/monsterhxw/docker-learning/blob/master/images/dockerpull.png)
##### 具体的选项可以通过 `docker pull --help` 命令看到
#### 2. 列出镜像其命令格式为：
```shell
[root@localhost ~]# docker image ls
[root@localhost ~]# docker images
```
![img](https://github.com/monsterhxw/docker-learning/blob/master/images/dockerimages.png)
#### 3. 删除镜像其命令格式为：
```shell
[root@localhost ~]# docker image rm [选项] <镜像1> [<镜像2> ...]
[root@localhost ~]# docker rmi [选项] <镜像1> [<镜像2> ...]
```
![img](https://github.com/monsterhxw/docker-learning/blob/master/images/dockerrmi.png)
## Docker 容器命令
#### 1、启动容器
#### 有两种方式，一种是基于镜像新建一个容器并启动，另外一个是将在终止状态（stopped）的容器重新启动。
##### 1.1 新建并启动其命令格式为：
```shell
[root@localhost ~]# docker run [选项] <镜像>
```
![img](https://github.com/monsterhxw/docker-learning/blob/master/images/dockerun.jpg)
##### 1.2 启动已终止容器其命令格式为：
```shell
[root@localhost ~]# docker start <容器ID或容器名>
```
![img](https://github.com/monsterhxw/docker-learning/blob/master/images/dockerstart.jpg)
#### 2. 守护态运行其命令格式为：
```shell
[root@localhost ~]# docker run -d <镜像>
```
![img](https://github.com/monsterhxw/docker-learning/blob/master/images/dockerrund.png)
#### 3. 终止容器其命令格式为：
```shell
[root@localhost ~]# docker stop <容器ID或容器名>
```
![img](https://github.com/monsterhxw/docker-learning/blob/master/images/dockerstop.png)
#### 4. 进入容器其命令格式为：
```shell
[root@localhost ~]# docker attach <容器ID或容器名>
[root@localhost ~]# docker exec [选项] <容器ID或容器名>
```
![img](https://github.com/monsterhxw/docker-learning/blob/master/images/dockerexec.png)
#### 5. 列出容器其命令格式为：
```shell
[root@localhost ~]# docker ps [选项]
[root@localhost ~]# docker container ls [选项]
```
![img](https://github.com/monsterhxw/docker-learning/blob/master/images/dockerps.png)
#### 5. 删除容器其命令格式为：
```shell
[root@localhost ~]# docker rm [选项] <容器ID/Name 1> [<容器ID/Name 2> ...]
[root@localhost ~]# docker container ls [选项]
```
![img](https://github.com/monsterhxw/docker-learning/blob/master/images/dockerps.png)
# 四、使用 Docker File 定制镜像
　　镜像的定制实际上就是定制每一层所添加的配置、文件。如果我们可以把每一层修改、安装、构建、操作的命令都写入一个脚本，这个脚本就是 Dockerfile。

　　Dockerfile 是一个文本文件，其内包含了一条条的指令(Instruction)，每一条指令构建一层，因此每一条指令的内容，就是描述该层应当如何构建。当我们需要定制自己额外的需求时，只需在 Dockerfile 上添加或者修改指令，重新生成 image 即可。

## Dockerfile 文件格式
```shell
# 1、第一行必须指定 基础镜像信息
FROM ubuntu
 
# 2、维护者信息
MAINTAINER docker_user docker_user@email.com
 
# 3、镜像操作指令
RUN echo "deb http://archive.ubuntu.com/ubuntu/ raring main universe" >> /etc/apt/sources.list
RUN apt-get update && apt-get install -y nginx
RUN echo "\ndaemon off;" >> /etc/nginx/nginx.conf
 
# 4、容器启动执行指令
CMD /usr/sbin/nginx
```

## Dockerfile 构建镜像
### 构建镜像的命令格式：
```shell
[root@localhost ~]# docker build [选项] <上下文路径/URL/->
```
#### 镜像构建的上下文（Context）

　　构建镜像会在 Docker 后台守护进程（daemon）中执行，而不是CLI中。构建前，构建进程会将 Dockerfile 在的路径下的全部内容（递归）发送到上传给 Docker daemon，Docker daemon 收到这个上下文包后，展开就会获得构建镜像所需的一切文件。 
### Dockerfile 指令详解
#### 1、FROM 指定基础镜像
　　定制镜像，那一定是以一个镜像为基础，在其上进行定制。FROM 就是指定基础镜像，因此一个 Dockerfile 中 FROM 是必备的指令，并且必须是第一条指令。
#### 2、RUN 执行命令
RUN 指令是用来执行命令行命令的。由于命令行的强大能力，RUN 指令在定制镜像时是最常用的指令之一。其格式有两种：

- shell 格式：RUN <命令>，就像直接在命令行中输入的命令一样。

	```shell
	RUN echo "deb http://archive.ubuntu.com/ubuntu/ raring main universe" >> /etc/apt/sources.list
	```

- exec 格式：`RUN ["可执行文件", "参数1", "参数2"]` 这更像是函数调用中的格式。

#### 3、COPY 复制文件
格式：

```shell
COPY <源路径>... <目标路径>
```
```shell
COPY package.json /usr/src/app/
```
#### 4、ADD 更高级的复制文件
ADD 指令和 COPY 的格式和性质基本一致。但是在 COPY 基础上增加了一些功能。

- <源路径> 可以是一个 URL
- ADD 指令将会自动解压缩 tar 压缩文件到 <目标路径> 

格式：

```shell
ADD <源路径>... <目标路径>
```
```shell
ADD ubuntu-xenial-core-cloudimg-amd64-root.tar.gz /
```
#### 5、WORKDIR 指定工作目录
使用 WORKDIR 指令可以来指定工作目录（或者称为当前目录），以后各层的当前目录就被改为指定的目录，如该目录不存在，WORKDIR 会帮你建立目录。

- <源路径> 可以是一个 URL
- ADD 指令将会自动解压缩 tar 压缩文件到 <目标路径> 

格式：

```shell
WORKDIR <工作目录路径>
```
```shell
WORKDIR /usr/local/tomcat/webapps/ROOT/
```
#### 6、VOLUME 定义匿名卷
事先指定某些目录挂载为匿名卷，这样在运行时如果用户不指定挂载，其应用也可以正常运行，不会向容器存储层写入大量数据。

格式：

```shell
VOLUME ["<路径1>", "<路径2>"...]
VOLUME <路径>
```
```shell
VOLUME /data
```
#### 7、EXPOSE 暴露端口
EXPOSE 指令是声明运行时容器提供服务端口，这只是一个声明，在运行时并不会因为这个声明应用就会开启这个端口的服务。

格式：

```shell
EXPOSE <端口1> [<端口2>...]
```
```shell
EXPOSE 8080
```
#### 8、还有一些指令可以参考下面地址
- [Dockerfie 官方文档](https://docs.docker.com/engine/reference/builder/)

## Dockerfile 构建镜像示例
### 1、创建 Dockerfile 文件
```shell
[root@localhost tomcat]# vi Dockerfile

FORM tomcat
WORKDIR /usr/local/tomcat/webapps/ROOT/
RUN rm -fr *
RUN echo "Hello Docker" >> index.html
WORKDIR /usr/local/tomcat

```
### 2、运行 docker build 命令
```shell
[root@localhost tomcat]# docker build -t test .
```
![img](https://github.com/monsterhxw/docker-learning/blob/master/images/dockerfile.png)
### 3、运行 docker run 命令
```shell
[root@localhost tomcat]# docker run -it -p 8080:8080 --rm test
```
![img](https://github.com/monsterhxw/docker-learning/blob/master/images/dockerfilerun.png)
### 4、在浏览器地址栏输入 http:ip 地址：8080 查看网页
![img](https://github.com/monsterhxw/docker-learning/blob/master/images/http.png)

# 五、Docker Compose
## Docker-compose 介绍
Compose 项目是 Docker 官方的开源项目，负责实现对 Docker 容器集群的快速编排。

Compose 定位是 「定义和运行多个 Docker 容器的应用（Defining and running multi-container Docker applications）」.

使用一个 Dockerfile 模板文件，可以让用户很方便的定义一个单独的应用容器。然而，在日常工作中，经常会碰到需要多个容器相互配合来完成某项任务的情况。例如要实现一个 Web 项目，除了 Web 服务容器本身，往往还需要再加上后端的数据库服务容器，甚至还包括负载均衡容器等。

Compose 它允许用户通过一个单独的 docker-compose.yml 模板文件来定义一组相关联的应用容器为一个项目（project）。

Compose 中有两个重要的概念：

- 服务 (service)：一个应用的容器，实际上可以包括若干运行相同镜像的容器实例。
- 项目 (project)：由一组关联的应用容器组成的一个完整业务单元，在 docker-compose.yml 文件中定义。

一个项目可以由多个服务（容器）关联而成，Compose 面向项目进行管理。

## Docker-compose 安装

### 1. 从 官方 GitHub Release 处直接下载编译好的二进制文件安装
```shell
[root@localhost ~]# curl -L https://github.com/docker/compose/releases/download/1.24.0-rc1/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
```
![img](https://github.com/monsterhxw/docker-learning/blob/master/images/dockercomposeloadi.png)

### 2. 为 docker-compose 添加执行权限
```shell
[root@localhost ~]# chmod +x /usr/local/bin/docker-compose
```
### 3. 检查 docker-compose 安装
```shell
[root@localhost ~]# docker-compose -version
```
## Docker Compose 命令
docker-compose 命令的基本的使用格式是：
```shell
[root@localhost ~]# docker-compose [-f=<arg>...] [options] [COMMAND] [ARGS...]
```
命令选项：

- -f, --file FILE 指定使用的 Compose 模板文件，默认为 docker-compose.yml，可以多次指定。
- -p, --project-name NAME 指定项目名称，默认将使用所在目录名称作为项目名。
- --x-networking 使用 Docker 的可拔插网络后端特性
- --x-network-driver DRIVER 指定网络后端的驱动，默认为 bridge
- --verbose 输出更多调试信息。
- -v, --version 打印版本并退出。

## Docker Compose 模板文件 指令
指令参考下面网址 

- [Docker Compose 模板文件 指令](http://www.funtl.com/zh/docker-compose/Docker-Compose-%E6%A8%A1%E6%9D%BF%E6%96%87%E4%BB%B6.html)

## Docker Compose 部署项目示例
### 1. 创建 docker-compose.yml 模板文件 
```shell
[root@localhost web]# vi docker-compose.yml

version: '3'
services:
  tomcat:
    restart: always
    image: tomcat
    container_name: web
    ports:
      - 8080:8080
    volumes:
      - /usr/local/docker/web/ROOT:/usr/local/tomcat/webapps/ROOT
  mysql:
    restart: always
    image: mysql:5.7.25
    container_name: mysql
    ports:
      - 3306:3306
    environment:
      TZ: Asia/Shanghai
      MYSQL_ROOT_PASSWORD: 123456
    command:
      --character-set-server=utf8mb4
      --collation-server=utf8mb4_general_ci
      --explicit_defaults_for_timestamp=true
      --lower_case_table_names=1
      --max_allowed_packet=128M
      --sql-mode="STRICT_TRANS_TABLES,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION,NO_ZERO_DATE,NO_ZERO_IN_DATE,ERROR_FOR_DIVISION_BY_ZERO"
    volumes:
      - mysql-data:/var/lib/mysql
volumes:
  mysql-data:

```
### 2. 执行 docker compose 命令
```shell
[root@localhost web]# docker-compose up -d 
```
### 3. 追踪项目执行信息
```shell
[root@localhost ~]# docker-compose logs -f web
```

