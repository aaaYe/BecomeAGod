

### Docker的基本组成

------

#### 镜像

Docker镜像（Image）就是一个只读的模板。镜像可以用来创建Docker容器，一个镜像可以创建很多容器。

#### 容器

Docker利用容器（Container）独立运行的一个或一组应用。容器是用镜像创建的运行实例。

它可以被启动、运行、停止、删除。每个容器都是相互隔离的、保证安全的平台。

可以把容器看成是一个简易版的Linux环境（包括root用户权限、进程空间、用户空间和网络空间等）和运行在其中的应用程序。

容器的定义和镜像几乎一模一样，也是一堆层的统一视角，唯一区别在于容器的最上面一层是可读可写的。

#### 仓库

仓库（Repository）是集中存放镜像文件的场所。

仓库（Repository）和仓库注册服务器（Registry）是有区别的。仓库注册服务器上往往存放着多个仓库，每个仓库又包含了多个镜像，每个镜像有不同的标签（tag）。

仓库分为公开仓库（public）和私有仓库（private）两种形式。

最大的仓库是Docker Hub（https://hub.docker.com）

存放了数量庞大的镜像供用户下载。国内的公开仓库包括阿里云、网易云等。

#### 总结

需要正确理解 仓库、镜像、容器这几个概念：

Docker本身是一个容器运行载体或称之为管理引擎。我们把运行环境和配置依赖打包好形成一个可交付的运行环境，这个打包好的运行环境就是image镜像文件。只有通过这个镜像文件才能生成Docker容器。image文件可以看作是容器的模板。Docker根据image文件生成容器的实例。同一个image文件，可以生成多个同时运行的容器实例。

image文件生成的容器实例，本身也是一个文件，称为镜像文件。

一个容器运行一种服务，当我们需要的时候，就可以通过docker客户端创建一个对应的运行实例，也就是我们的容器。

至于仓库，就是堆放了一堆镜像的地方，我们可以把镜像发布到仓库中，需要的时候从仓库拉下来就可以了。



### CentOS7安装docker

------

官网中文安装手册（卡得一批）https://docs.docker-cn.com/engine/installation/linux/docker-ce/centos/#prerequisites

#### 使用官方安装脚本自动安装

```shell
curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun
```

也可以使用国内daocloud一键安装命令

```shell
curl -sSL https://get.daocloud.io/docker | sh
```

#### 手动安装

```
1.卸载旧版本
$ sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
我从B站看来的
yum -y remove docker docker-common docker-selinux docker-engine

2.万年的gcc相关
yum -y install gcc
yum -y install gcc-c++

3.安装需要的软件包
yum install -y yum-utils device-mapper-persistent-data lvm2

4.设置stable镜像库
大坑
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
推荐
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

5.更新yum索引
yum makecache fast

6.安装docker-ce
yum -y install docker-ce

7.启动docker
systemctl start docker

8.测试
docker version
docker run hello-world

9.配置镜像加速
mkdir -p /etc/docker
vim  /etc/docker/daemon.json
  	{
		"registry-mirrors": ["http://hub-mirror.c.163.com"]
	}
	{
		"registry-mirrors": ["https://pj5ust8e.mirror.aliyuncs.com"]
	}

centos6.8
vim /etc/sysconfig/docker
other_args="--registry-mirror=https://pj5ust8e.mirror.aliyuncs.com"

systemctl daemon-reload
systemctl restart docker

10.卸载
systemctl stop docker
yum -y remove docker-ce
rm -rf /var/lib/docker
```

### Docker是怎么工作的

Docker是一个client-server结构的系统，Docker守护进程运行在主机上，然后通过Socket连接从客户端访问，守护进程从客户端接收命令并管理运行在主机上的容器。容器，是一个运行时环境，就是集装箱。