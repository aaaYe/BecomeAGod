# 配置静态网卡

1.Vmware Workstation NAT模式

首页-编辑-虚拟网络编辑器(能看到子网IP 192.168.44.0)

![image-20210113205346437](..\images\image-20210113205346437.png)

```shell
#root用户进入虚拟机修改网卡文件
vi /etc/sysconfig/network-scripts/ifcfg-ens33
#能看到文件
TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=dhcp
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=ens33
UUID=2f7213c0-d7ba-4804-9ba4-0af612e01d18
DEVICE=ens33
ONBOOT=yes
#修改
BOOTPROTO=static
#在后面添加
IPADDR="192.168.44.13"
NETMASK="255.255.255.0"
GATEWAY="192.168.44.2"
ZONE=public
#wq保存
#重启网卡centos8
nmcli c reload
#重启网卡centos7
systemctl restart network
```

