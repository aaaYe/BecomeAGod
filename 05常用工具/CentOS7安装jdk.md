```shell
#1.Oracle官网下载jdk
#2.解压
tar -zxvf jdk-15.0.1_linux-x64_bin.tar.gz
#3.mv到/usr/local/
mv jdk-15.0.1 /usr/local/
#4.配置环境变量
vim /etc/profile
#末尾添加
#java
export JAVA_HOME=/usr/local/jdk-15.0.1/
export CLASSPATH=.:$JAVA_HOME/jre/lib/rt.jar:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
export PATH=$PATH:$JAVA_HOME/bin
#5
source /etc/profile
java -version
```

