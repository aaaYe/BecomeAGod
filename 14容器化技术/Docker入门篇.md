> Docker学习

- Docker概述
- Docker安装
- Docker命令
- - 镜像命令
  - 容器命令
  - 操作命令
  - ...
- Docker镜像！
- 容器数据卷！
- DockerFile
- Docker网络原理
- IDEA整合Docker
- Docker Compose
- Docker Swarm
- CI\CD Jenkins

> 你知道的越多，你不知道的就越多

# Docker概述

## Docker为什么出现？

一款产品：开发-运维 两套环境！应用环境，应用配置。

开发-运维问题：在开发电脑上可以运行，在生产环境无法运行，导致版本不可用，对运维压力非常大。

环境配置是一件十分麻烦的事情，每一个机器都要搭建环境（redis集群、ES、Hadoop），费时费力。

发布一个项目（jar+redis+ES+Hadoop...）。

传统：开发jar，运维来做。

现在：开发打包部署上线，一套流程做完。



java -- apk -- 发布（应用商店）

java -- jar（环境）-- 打包项目带上环境（镜像）-- Docker仓库：商店 -- 下载发布的镜像运行



Docker给以上的问题，提供了解决方案！

## Docker的历史

2010年，几个搞IT的年轻人，在美国成立了一家公司`dotCloud`

做一些 pass 的云计算服务！LXC有关的容器技术！

他们将自己技术（容器技术）命名为docker。

Docker刚刚诞生的时候，没有引起行业的注意，dotCloud活不下去了！

`开源`

开源源代码！2013年，docker开源！

Docker越来越多的人发现了docker的优点！火了，Docker每一个月会更新一个版本！

2014年4月9日，Docker1.0发布！

Docker为什么这么火？十分的轻巧！

在容器技术出来之前，我们都是使用的虚拟机技术！

虚拟机：在windows中装一个Vmware，通过这个软件我们可以虚拟出一台或多台电脑，十分笨重！

虚拟机也是属于虚拟化技术，Docker容器技术，也是一种虚拟化技术！

```
vm:linux centos原生镜像(一台电脑!)隔离,需要开启多个虚拟机!	几个G	几分钟
docker:隔离,镜像(最核心的环境 4m+jdk+mysql)十分轻巧,运行镜像就可以了!	几个M KB 秒级启动!
```

> 聊聊docker

Docker是基于Go语言开发的开源项目

官网：https://www.docker.com/

## Docker能做什么

> 之前的虚拟机技术！

<img src="..\images\15互联网前沿技术-Docker入门篇-虚拟机技术.png" alt="之前的虚拟机技术" style="zoom:60%;" />

虚拟机技术缺点：

1. 资源占用多
2. 冗余步骤多
3. 启动慢

> 容器化技术

`容器化技术不是模拟的一个完整的操作系统`

<img src="..\images\15互联网前沿技术-Docker入门篇-容器化技术.png" alt="容器化技术" style="zoom:60%;" />

比较docker和虚拟机技术的不同：

- 传统虚拟机，虚拟出一套硬件，运行一个完整的操作系统，然后在这个操作系统上安装和运行软件
- 容器的应用直接运行在宿主机的内容，容器是没有自己的内核的，也没有虚拟硬件，所以就轻便了
- 每个容器间是相互隔离的，每个容器内有自己的文件系统，互不影响

> DevOps(开发、运维)

**应用更快速的交付和部署**

传统：一堆帮助文档，安装程序

Docker：打包镜像，发布测试，一键运行

**更快捷的升级和缩容**

使用了Docker之后，我们部署应用就和搭积木一样！

(Springboot1.5	Redis5	tomcat8)升级！

项目打包为一个镜像，扩展	服务器A，服务器B

**更简单的系统运维**

在容器化之后，我们的开发，测试，生成环境高度一致！

**更高效的计算资源利用**

Docker是 内核级别的虚拟化，在一个物理机上可以运行很多的容器实例！服务器的性能可以被压榨到极致！

# Docker安装

## Docker的基本组成

![img](..\images\15互联网前沿技术-Docker入门篇-Docker的基本组成.jpg)

**镜像(image):**

docker镜像好比是一个模板，可以通过这个模板来创建容器服务，tomcat镜像==> run ==> tomcat01容器(提供服务器)，通过这个镜像可以创建多个容器(最终服务运行或项目运行就是在容器中)。

**容器(container):**

docker利用容器技术，独立运行一个或一组应用，通过镜像来创建。

启动，停止，删除，基本命令！

目前这个容器可以理解为一个简易的Linux系统。

**仓库(repository):**

仓库就是存放镜像的地方！

仓库分为私有仓库和公有仓库！

Docker Hub

阿里云......都有容器服务器(配置镜像加速！)

## 安装Docker

> 环境准备

1. 需要Linux基础
2. CentOS7
3. 使用xshell连接远程服务器操作

> 环境查看

```shell
#系统内核是3.10以上
[root@docker ~]# uname -r
3.10.0-693.el7.x86_64
```

```shell
[root@docker ~]# cat /etc/os-release 
NAME="CentOS Linux"
VERSION="7 (Core)"
ID="centos"
ID_LIKE="rhel fedora"
VERSION_ID="7"
PRETTY_NAME="CentOS Linux 7 (Core)"
ANSI_COLOR="0;31"
CPE_NAME="cpe:/o:centos:centos:7"
HOME_URL="https://www.centos.org/"
BUG_REPORT_URL="https://bugs.centos.org/"

CENTOS_MANTISBT_PROJECT="CentOS-7"
CENTOS_MANTISBT_PROJECT_VERSION="7"
REDHAT_SUPPORT_PRODUCT="centos"
REDHAT_SUPPORT_PRODUCT_VERSION="7"
```

> 安装

帮助文档：https://docs.docker.com/engine/install/

```shell
# 1、卸载旧版本
yum remove docker \
           docker-client \
           docker-client-latest \
           docker-common \
           docker-latest \
           docker-latest-logrotate \
           docker-logrotate \
           docker-engine
           
# 2、需要的软件包
yum install -y yum-utils

# 3、设置镜像仓库
# 默认是国外的！
yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
# 推荐使用阿里云镜像仓库
yum-config-manager \
    --add-repo \
	http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
	
# 更新yum软件包索引
yum makecache

# 4、安装docker-ce(社区版) 	ee(企业版)
yum install docker-ce docker-ce-cli containerd.io

# 5、启动docker
systemctl start docker

# 6、docker version查看是否安装成功
[root@docker ~]# docker version
Client: Docker Engine - Community
 Version:           20.10.2
 API version:       1.41
 Go version:        go1.13.15
 Git commit:        2291f61
 Built:             Mon Dec 28 16:17:48 2020
 OS/Arch:           linux/amd64
 Context:           default
 Experimental:      true

Server: Docker Engine - Community
 Engine:
  Version:          20.10.2
  API version:      1.41 (minimum version 1.12)
  Go version:       go1.13.15
  Git commit:       8891c58
  Built:            Mon Dec 28 16:16:13 2020
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.4.3
  GitCommit:        269548fa27e0089a8b8278fc4fc781d7f65a939b
 runc:
  Version:          1.0.0-rc92
  GitCommit:        ff819c7e9184c13b7c2607fe6c30ae19403a7aff
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0

# 7、docker run hello-world
[root@docker ~]# docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
0e03bdcc26d7: Pull complete 
Digest: sha256:1a523af650137b8accdaed439c17d684df61ee4d74feac151b5b337bd29e7eec
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/

# 8、查看一下下载的hello-world镜像
[root@docker ~]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
hello-world   latest    bf756fb1ae65   12 months ago   13.3kB
```

了解：卸载docker

```shell
# 1、卸载依赖
yum remove docker-ce docker-ce-cli containerd.io

# 2、删除资源(docker的默认工作路径)
rm -rf /var/lib/docker
```

## 配置阿里云镜像加速

阿里云--控制台--容器镜像服务--镜像加速器

![15互联网前沿技术-Docker入门篇-阿里云镜像加速器](..\images\15互联网前沿技术-Docker入门篇-阿里云镜像加速器.png)

配置使用

```shell
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://pj5ust8e.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

> 回顾hello-world流程

```shell
[root@docker ~]# docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
0e03bdcc26d7: Pull complete 
Digest: sha256:1a523af650137b8accdaed439c17d684df61ee4d74feac151b5b337bd29e7eec
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:

  1. The Docker client contacted the Docker daemon.
  2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
     (amd64)
  3. The Docker daemon created a new container from that image which runs the
     executable that produces the output you are currently reading.
  4. The Docker daemon streamed that output to the Docker client, which sent it
     to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```

docker run的流程图

![image-20210108194030421](..\images\image-20210108194030421.png)

## 底层原理

**Docker怎么工作的？**

Docker是一个Client-Server结构的系统，Docker的守护进程运行在主机上，通过Socket从客户端去访问！

DockerServer接收到Docker-Client的指令，就会执行这个命令！

![image-20210108194701227](..\images\image-20210108194701227.png)

**Docker为什么比虚拟机快？**

1.Docker有着比虚拟机更少的抽象层(虚拟硬件)。

2.docker利用的是宿主的内核，vm需要的是Guest OS。

![image-20210108194833516](..\images\image-20210108194833516.png)

所以说，新建一个容器的时候，docker不需要像虚拟机一样重新加载一个操作系统内核，避免引导。虚拟机是加载Guest OS，分钟级别的，而docker是利用宿主机的操作系统，省略了这个复杂的过程，秒级！

![image-20210108205856678](..\images\image-20210108205856678.png)

# Docker的常用命令

## 帮助命令

```shell
docker version	# 显示docker的版本信息
docker info		# 显示docker的系统信息,包括镜像和容器的数量
docker 命令 --help	# 帮助命令
```

帮助文档地址：https://docs.docker.com/engine/reference/commandline/docker/

## 镜像命令

**docker images**	查看所有本地主机上的镜像

```shell
[root@docker ~]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
hello-world   latest    bf756fb1ae65   12 months ago   13.3kB

# 解释
REPOSITORY	镜像的仓库源
TAG			镜像的标签
IMAGE ID	镜像的ID
CREATED		镜像的创建时间
SIZE		镜像的大小
# 可选参数
[root@docker ~]# docker images --help

Usage:  docker images [OPTIONS] [REPOSITORY[:TAG]]

List images

Options:
  -a, --all             Show all images (default hides intermediate images)
      --digests         Show digests
  -f, --filter filter   Filter output based on conditions provided
      --format string   Pretty-print images using a Go template
      --no-trunc        Don't truncate output
  -q, --quiet           Only show image IDs.只显示镜像ID
```

**docker search**	搜索镜像

```shell
[root@docker ~]# docker search mysql
NAME                              DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
mysql                             MySQL is a widely used, open-source relation…   10357     [OK]       
mariadb                           MariaDB is a community-developed fork of MyS…   3838      [OK]       
mysql/mysql-server                Optimized MySQL Server Docker images. Create…   757                  [OK]
percona                           Percona Server is a fork of the MySQL relati…   519       [OK]       
centos/mysql-57-centos7           MySQL 5.7 SQL database server                   86                   
mysql/mysql-cluster               Experimental MySQL Cluster Docker images. Cr…   79                   
centurylink/mysql                 Image containing mysql. Optimized to be link…   60                   [OK]
bitnami/mysql                     Bitnami MySQL Docker Image                      47                   [OK]
deitch/mysql-backup               REPLACED! Please use http://hub.docker.com/r…   41                   [OK]
tutum/mysql                       Base docker image to run a MySQL database se…   35                   
prom/mysqld-exporter                                                              34                   [OK]
databack/mysql-backup             Back up mysql databases to... anywhere!         34                   
schickling/mysql-backup-s3        Backup MySQL to S3 (supports periodic backup…   29                   [OK]
linuxserver/mysql                 A Mysql container, brought to you by LinuxSe…   27                   
centos/mysql-56-centos7           MySQL 5.6 SQL database server                   20                   
circleci/mysql                    MySQL is a widely used, open-source relation…   19                   
mysql/mysql-router                MySQL Router provides transparent routing be…   17                   
arey/mysql-client                 Run a MySQL client from a docker container      16                   [OK]
fradelg/mysql-cron-backup         MySQL/MariaDB database backup using cron tas…   10                   [OK]
openshift/mysql-55-centos7        DEPRECATED: A Centos7 based MySQL v5.5 image…   6                    
devilbox/mysql                    Retagged MySQL, MariaDB and PerconaDB offici…   3                    
ansibleplaybookbundle/mysql-apb   An APB which deploys RHSCL MySQL                2                    [OK]
jelastic/mysql                    An image of the MySQL database server mainta…   1                    
widdpim/mysql-client              Dockerized MySQL Client (5.7) including Curl…   1                    [OK]
monasca/mysql-init                A minimal decoupled init container for mysql    0

# 可选项,通过收藏来过滤
[root@docker ~]# docker search --help

Usage:  docker search [OPTIONS] TERM

Search the Docker Hub for images

Options:
  -f, --filter filter   Filter output based on conditions provided
      --format string   Pretty-print search using a Go template
      --limit int       Max number of search results (default 25)
      --no-trunc        Don't truncate outpu
# --filter=STARS=3000 #搜索出来的镜像是收藏STARS大于3000的
```

**docker pull**	下载镜像

```shell
# 下载镜像 docker pull 镜像名[:tag]
[root@docker ~]# docker pull mysql
Using default tag: latest	#如果不写tag,默认就是latest
latest: Pulling from library/mysql
6ec7b7d162b2: Pull complete 	#分层下载	docker image的核心	联合文件系统
fedd960d3481: Pull complete 
7ab947313861: Pull complete 
64f92f19e638: Pull complete 
3e80b17bff96: Pull complete 
014e976799f9: Pull complete 
59ae84fee1b3: Pull complete 
ffe10de703ea: Pull complete 
657af6d90c83: Pull complete 
98bfb480322c: Pull complete 
6aa3859c4789: Pull complete 
1ed875d851ef: Pull complete 
Digest: sha256:78800e6d3f1b230e35275145e657b82c3fb02a27b2d8e76aac2f5e90c1c30873	#签名
Status: Downloaded newer image for mysql:latest
docker.io/library/mysql:latest	#真实地址

# 等价于它
docker pull mysql
docker pull docker.io/library/mysql:latest

# 指定版本
[root@docker ~]# docker pull mysql:5.7
5.7: Pulling from library/mysql
6ec7b7d162b2: Already exists 
fedd960d3481: Already exists 
7ab947313861: Already exists 
64f92f19e638: Already exists 
3e80b17bff96: Already exists 
014e976799f9: Already exists 
59ae84fee1b3: Already exists 
7d1da2a18e2e: Pull complete 
301a28b700b9: Pull complete 
529dc8dbeaf3: Pull complete 
bc9d021dc13f: Pull complete 
Digest: sha256:c3a567d3e3ad8b05dfce401ed08f0f6bf3f3b64cc17694979d5f2e5d78e10173
Status: Downloaded newer image for mysql:5.7
docker.io/library/mysql:5.7
```

**docker rmi**	删除镜像

```shell
# 删除指定id的镜像
[root@docker ~]# docker rmi -f f07dfa83b528
# 删除多个id的镜像
[root@docker ~]# docker rmi -f f07dfa83b528 f07dfa83b528 f07dfa83b528
# 删除全部容器
[root@docker ~]# docker rmi -f $(docker images -aq)
```

## 容器命令

说明：我们有了镜像才可以创建容器，Linux，下载一个centos镜像来学习！

```shell
docker pull centos
```

**新建容器并启动**

```shell
docker run [可选参数] image
# 参数说明
--name="Name"	容器名字	tomcat01	tomcat02,用来区分容器
-d				后台方式运行
-it				使用交互方式运行，进入容器查看内容
-p				指定容器端口	-p	8080:8080
	-p ip:主机端口:容器端口
	-p 主机端口:容器端口(常用)
	-p 容器端口
-p				随机指定端口

# 测试，启动并进入容器
[root@docker ~]# docker run -it centos /bin/bash
[root@48b8c32d48fd /]# 
# 从容器中退回主机
[root@48b8c32d48fd /]# exit
exit
[root@docker ~]# 
```

**列出所有运行的容器**

```shell
# docker ps
-a		# 列出当前正在运行的容器+历史运行过的容器
-n=?	# 列出最近创建的n个容器
-q		# 只显示容器的编号

[root@docker ~]# docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
[root@docker ~]# docker ps -a
CONTAINER ID   IMAGE         COMMAND       CREATED         STATUS                          PORTS     NAMES
48b8c32d48fd   centos        "/bin/bash"   2 minutes ago   Exited (0) About a minute ago             laughing_swartz
eb10382d5919   hello-world   "/hello"      20 hours ago    Exited (0) 20 hours ago                   gallant_bardeen
[root@docker ~]# 

```

**退出容器**

```
exit	# 直接退出容器并停止
Ctrl+P+Q	# 容器不停止退出
```

**删除容器**

```shell
docker rm 容器id	# 删除指定id容器，不能删除正在运行的容器，如果要强制删除 -f
docker rm -f $(docker ps -aq)	#删除所有容器
docker ps -a -q|xargs docker rm	#删除所有容器
```

**启动和停止容器的操作**

```shell
docker start 容器id	#启动容器
docker restart 容器id	#重启容器
docker stop 容器id	#停止当前正在运行的容器
docker kill 容器id	#强制停止当前容器
```

## 常用其他命令

**后台启动**

```shell
#命令 docker run -d 镜像名
[root@docker ~]# docker run -d centos
6715baae521130453abe1e0e8ababd267f01b6b1de489143abb1670fab72ee25
#问题 docker ps 的时候，发现centos停止了
#常见的坑，docker 容器使用后台运行时，就必须要有一个前台进程，docker发现没有应用，就会自动停止
#nginx 容器启动后，发现自己没有提供服务，就会立刻停止，就是没有程序了
```

**查看日志**

```shell
docker logs -f -t --tail 容器，没有日志
#自己编写一段shell脚本
[root@docker ~]# docker run -d centos /bin/sh -c "while true;do echo kuangshen;sleep 1;done"
d91629c67850e6c90feda5d5a87a6034987dfb874a3ebec88d99cff74a965759
[root@docker ~]# docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS     NAMES
d91629c67850   centos    "/bin/sh -c 'while t…"   6 seconds ago   Up 5 seconds             epic_cannon
#显示日志
-tf #显示日志
--tail number	#显示日志条数
[root@docker ~]# docker logs -tf --tail 10 d91629c67850
2021-01-09T07:40:08.416131865Z kuangshen
2021-01-09T07:40:09.419272206Z kuangshen
2021-01-09T07:40:10.421734722Z kuangshen
2021-01-09T07:40:11.445675004Z kuangshen
2021-01-09T07:40:12.452247287Z kuangshen
2021-01-09T07:40:13.457497257Z kuangshen
2021-01-09T07:40:14.459807265Z kuangshen
2021-01-09T07:40:15.462516714Z kuangshen
2021-01-09T07:40:16.465481006Z kuangshen
2021-01-09T07:40:17.468874126Z kuangshen
2021-01-09T07:40:18.471875571Z kuangshen
2021-01-09T07:40:19.477325470Z kuangshen
2021-01-09T07:40:20.479920603Z kuangshen
2021-01-09T07:40:21.483116295Z kuangshen
2021-01-09T07:40:22.487122085Z kuangshen
2021-01-09T07:40:23.489834614Z kuangshen
2021-01-09T07:40:24.492189842Z kuangshen
2021-01-09T07:40:25.497229021Z kuangshen
2021-01-09T07:40:26.501277258Z kuangshen
2021-01-09T07:40:27.504147399Z kuangshen
2021-01-09T07:40:28.507532513Z kuangshen
2021-01-09T07:40:29.509282552Z kuangshen
2021-01-09T07:40:30.512583781Z kuangshen
^C
[root@docker ~]# 
```

**查看容器中的进程信息**	ps

```shell
# docker top 容器id
[root@docker ~]# docker top d91629c67850
UID   PID   PPID   C  STIME   TTY   TIME 
root  1623  1603   0  15:39    ?    00:00:00           
root  1973  1623   0  15:44    ?    00:00:00
```

**查看镜像的元数据**

```shell
#命令 docker inspect 容器id
[root@docker ~]# docker inspect d91629c67850
[
    {
        "Id": "d91629c67850e6c90feda5d5a87a6034987dfb874a3ebec88d99cff74a965759",
        "Created": "2021-01-09T07:39:41.865078341Z",
        "Path": "/bin/sh",
        "Args": [
            "-c",
            "while true;do echo kuangshen;sleep 1;done"
        ],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 1623,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2021-01-09T07:39:42.338925096Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:300e315adb2f96afe5f0b2780b87f28ae95231fe3bdd1e16b9ba606307728f55",
        "ResolvConfPath": "/var/lib/docker/containers/d91629c67850e6c90feda5d5a87a6034987dfb874a3ebec88d99cff74a965759/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/d91629c67850e6c90feda5d5a87a6034987dfb874a3ebec88d99cff74a965759/hostname",
        "HostsPath": "/var/lib/docker/containers/d91629c67850e6c90feda5d5a87a6034987dfb874a3ebec88d99cff74a965759/hosts",
        "LogPath": "/var/lib/docker/containers/d91629c67850e6c90feda5d5a87a6034987dfb874a3ebec88d99cff74a965759/d91629c67850e6c90feda5d5a87a6034987dfb874a3ebec88d99cff74a965759-json.log",
        "Name": "/epic_cannon",
        "RestartCount": 0,
        "Driver": "overlay2",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": null,
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "default",
            "PortBindings": {},
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "CapAdd": null,
            "CapDrop": null,
            "CgroupnsMode": "host",
            "Dns": [],
            "DnsOptions": [],
            "DnsSearch": [],
            "ExtraHosts": null,
            "GroupAdd": null,
            "IpcMode": "private",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 0,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": null,
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "ConsoleSize": [
                0,
                0
            ],
            "Isolation": "",
            "CpuShares": 0,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "",
            "BlkioWeight": 0,
            "BlkioWeightDevice": [],
            "BlkioDeviceReadBps": null,
            "BlkioDeviceWriteBps": null,
            "BlkioDeviceReadIOps": null,
            "BlkioDeviceWriteIOps": null,
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
            "KernelMemory": 0,
            "KernelMemoryTCP": 0,
            "MemoryReservation": 0,
            "MemorySwap": 0,
            "MemorySwappiness": null,
            "OomKillDisable": false,
            "PidsLimit": null,
            "Ulimits": null,
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0,
            "MaskedPaths": [
                "/proc/asound",
                "/proc/acpi",
                "/proc/kcore",
                "/proc/keys",
                "/proc/latency_stats",
                "/proc/timer_list",
                "/proc/timer_stats",
                "/proc/sched_debug",
                "/proc/scsi",
                "/sys/firmware"
            ],
            "ReadonlyPaths": [
                "/proc/bus",
                "/proc/fs",
                "/proc/irq",
                "/proc/sys",
                "/proc/sysrq-trigger"
            ]
        },
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/b4874e48439f9f7ed6e9e6ce883ede84ad024f71736c35585c548aae0e436502-init/diff:/var/lib/docker/overlay2/68016317d078c92e8c9392868e46dddc34d873a23c80b2f094b1d9483852c94e/diff",
                "MergedDir": "/var/lib/docker/overlay2/b4874e48439f9f7ed6e9e6ce883ede84ad024f71736c35585c548aae0e436502/merged",
                "UpperDir": "/var/lib/docker/overlay2/b4874e48439f9f7ed6e9e6ce883ede84ad024f71736c35585c548aae0e436502/diff",
                "WorkDir": "/var/lib/docker/overlay2/b4874e48439f9f7ed6e9e6ce883ede84ad024f71736c35585c548aae0e436502/work"
            },
            "Name": "overlay2"
        },
        "Mounts": [],
        "Config": {
            "Hostname": "d91629c67850",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/sh",
                "-c",
                "while true;do echo kuangshen;sleep 1;done"
            ],
            "Image": "centos",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {
                "org.label-schema.build-date": "20201204",
                "org.label-schema.license": "GPLv2",
                "org.label-schema.name": "CentOS Base Image",
                "org.label-schema.schema-version": "1.0",
                "org.label-schema.vendor": "CentOS"
            }
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "b6b740f70cf62195986323aca7b263b0cbd158d8fd6eafc717a945f8048dd1d1",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {},
            "SandboxKey": "/var/run/docker/netns/b6b740f70cf6",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "c9bff1af2ff03cf1fd3e7af90c998f4da48f2e5af44152ca3c69b70ac15718ba",
            "Gateway": "172.17.0.1",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "172.17.0.2",
            "IPPrefixLen": 16,
            "IPv6Gateway": "",
            "MacAddress": "02:42:ac:11:00:02",
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "e8625976390355cb53a3d37b40aad7c763a60d164030349c0a391b6ef9933db5",
                    "EndpointID": "c9bff1af2ff03cf1fd3e7af90c998f4da48f2e5af44152ca3c69b70ac15718ba",
                    "Gateway": "172.17.0.1",
                    "IPAddress": "172.17.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:11:00:02",
                    "DriverOpts": null
                }
            }
        }
    }
]
[root@docker ~]# 
```

**进入当前正在运行的容器**

```shell
#通常容器都是后台运行的，需要进入容器，修改一些配置
# 命令
docker exec -it 容器id bashShell
[root@docker ~]# docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS          PORTS     NAMES
d91629c67850   centos    "/bin/sh -c 'while t…"   28 minutes ago   Up 28 minutes             epic_cannon
[root@docker ~]# docker exec -it d91629c67850 /bin/bash
[root@d91629c67850 /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
[root@d91629c67850 /]# ps -ef
UID         PID   PPID  C STIME TTY          TIME CMD
root          1      0  0 07:39 ?        00:00:00 /bin/sh -c while true;do echo kuangshen;sleep 1;done
root       1734      0  0 08:08 pts/0    00:00:00 /bin/bash
root       1756      1  0 08:08 ?        00:00:00 /usr/bin/coreutils --coreutils-prog-shebang=sleep /usr/bin/sleep 1
root       1757   1734  0 08:08 pts/0    00:00:00 ps -ef
[root@d91629c67850 /]# 

#方式二
docker attach 容器di
#正在执行当前的代码...
#docker exec	进入容器后开启一个新的终端，可以在里面进行操作
#docker attach	进入容器正在执行的终端，不会启动新的进程
```

**从容器内拷贝文件到主机上**

```shell
docker cp 容器id:容器内路径 目的的主机路径

[root@docker ~]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
mysql         latest    a347a5928046   2 weeks ago     545MB
centos        latest    300e315adb2f   4 weeks ago     209MB
hello-world   latest    bf756fb1ae65   12 months ago   13.3kB
[root@docker ~]# docker run -it centos /bin/bash
[root@2d0a513eda86 /]# cd /home/
[root@2d0a513eda86 home]# touch kuangshen.java
[root@2d0a513eda86 home]# ls
kuangshen.java
[root@2d0a513eda86 home]# exit
exit
[root@docker ~]# docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
[root@docker ~]# docker ps -a
CONTAINER ID   IMAGE         COMMAND       CREATED              STATUS                         PORTS     NAMES
2d0a513eda86   centos        "/bin/bash"   About a minute ago   Exited (0) 10 seconds ago                magical_gagarin
6715baae5211   centos        "/bin/bash"   54 minutes ago       Exited (0) 54 minutes ago                relaxed_grothendieck
48b8c32d48fd   centos        "/bin/bash"   About an hour ago    Exited (0) About an hour ago             laughing_swartz
eb10382d5919   hello-world   "/hello"      21 hours ago         Exited (0) 21 hours ago                  gallant_bardeen
[root@docker ~]# docker cp 2d0a513eda86:/home/kuangshen.java /home
[root@docker ~]# cd /home/
[root@docker home]# ll
总用量 0
-rw-r--r--. 1 root root  0 1月   9 16:15 kuangshen.java
drwx------. 2 liye liye 62 1月   6 15:19 liye
[root@docker home]# 

#拷贝是一个手动过程，未来使用 -v 卷的技术，可以实现
```

## 小结

![image-20210109194742165](..\images\image-20210109194742165.png)

## 作业练习

> Docker 安装Nginx

```shell
#1、搜索镜像	docker hub上有帮助文档
docker search 
#2、下载镜像
docker pull
# -d 后台运行
# --name 给容器命名
# -p 宿主机端口:容器内部端口
[root@docker ~]# docker run -d --name nginx01 -p 3344:80 nginx
c83c4c6403a630422a7140608f8fe577d5e7507a38f157381a8e5ff843668b19
[root@docker ~]# docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS         PORTS                  NAMES
c83c4c6403a6   nginx     "/docker-entrypoint.…"   11 seconds ago   Up 9 seconds   0.0.0.0:3344->80/tcp   nginx01
[root@docker ~]# curl localhost:3344

#进入容器
[root@docker ~]# docker exec -it nginx01 /bin/bash
root@c83c4c6403a6:/# whereis nginx
nginx: /usr/sbin/nginx /usr/lib/nginx /etc/nginx /usr/share/nginx
root@c83c4c6403a6:/# cd /etc/nginx/
root@c83c4c6403a6:/etc/nginx# ll
bash: ll: command not found
root@c83c4c6403a6:/etc/nginx# ls
conf.d	fastcgi_params	koi-utf  koi-win  mime.types  modules  nginx.conf  scgi_params	uwsgi_params  win-utf
root@c83c4c6403a6:/etc/nginx# 
```

问题：我们每次修改nginx配置文件，都需要进入到容器内部？十分麻烦，我要是可以在容器外部提供一个映射路径，达到在容器修改文件名，容器内部就可以自动修改？ -v数据卷！

> Docker安装一个tomcat

```shell
#官方的使用
docker run -it --rm tomcat:9.0
#我们之前的启动都是后台，停止了容器之后，容器还是可以查到 docker run -it --rm，一般用来测试，用完删除

#下载再启动
docker pull tomcat
docker run -d -p 8080:8080 --name tocmat01 tomcat

#访问tomcat发现404,进入容器
[root@docker ~]# docker run -d -p 8080:8080 --name tomcat01 tomcat
b585bd2f88e5223438dabd80c573369a91f390809e37a4f74dee6e82f3c2233d
[root@docker ~]# docker exec -it tomcat01 /bin/bash
root@b585bd2f88e5:/usr/local/tomcat# ls
BUILDING.txt  CONTRIBUTING.md  LICENSE	NOTICE	README.md  RELEASE-NOTES  RUNNING.txt  bin  conf  lib  logs  native-jni-lib  temp  webapps  webapps.dist  work
root@b585bd2f88e5:/usr/local/tomcat# 
#发现问题:1、linux的命令少了。2、webapps里面没有文件。镜像的原因:默认是最小的镜像,所以不必要的都删除掉
#保证最小可运行环境
root@b585bd2f88e5:/usr/local/tomcat# cp -r webapps.dist/* webapps
root@b585bd2f88e5:/usr/local/tomcat# 
```

**思考问题：我们以后要部署项目，如果每次都要进入容器十分麻烦！我要是可以在容器外提供一个映射路径，webapps，我们在外部放置项目，就自动同步到内部就好了！**

> 部署ES+kibana