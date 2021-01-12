### 1.安装需要的环境

```shell
yum install gcc-c++
yum install -y pcre pcre-devel
yum install -y zlib zlib-devel
yum install -y openssl openssl-devel
```

### 2.下载安装包

[nginx下载地址](http://nginx.org/en/download.html)

### 3.解压编译安装

上传到/usr/local/

解压编译安装

```shell
tar -zxvf nginx-1.18.0.tar.gz
cd nginx-1.18.0
./configure
make
make install
```

安装好的nginx可以用 whereis命令 查询。