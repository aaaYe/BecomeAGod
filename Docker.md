> Docker学习

------

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