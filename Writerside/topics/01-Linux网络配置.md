# 01-Linux网络配置

## 1. 主机名配置

/etc/hosts是配置IP地址和对应主机名的文件，记录本机或其他主机的IP地址及其所对应的主机名。

```shell
vim /etc/hosts

# 修改文件为以下内容
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
#  添加jxyy主机对应的ip
127.0.0.1   jxyy.com    jxyy
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6

esc + :wq  #保存退出


ping jxyy.com  # 测试是否修改成
```

## 2. 配置静态IP

``` shell
cd /etc/sysconfig/network-scripts/  # 进入到网络配置文件目录

vim ifcfg-ens33  # 修改ens33网卡配置文件

# 修改以下内容
BOOTPROTO="dhcp"   改为  BOOTPROTO="static" 
# 最后面添加以下内容
IPADDR=192.168.110.101
GATEWAY=192.168.110.2  #  vmware虚拟网络编辑器 -》 NAT设置 里面查看网关
NETMASK=255.255.255.0
# ip地址的第三个数字是网段，要和你们的网关的网段一样，最后一个数字不要设置255和0-10.

esc + :wq  # 保存退出

# 重启网卡
systemctl restart network

# 查看是否配置成功
ifconfig

##  ens33的ip已经变化
ens33: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.110.101  netmask 255.255.255.0  broadcast 192.168.110.255
```

## 3. 一张网卡多个ip

本质是把网卡虚拟成更多的网卡

```shell
cd /etc/sysconfig/network-scripts/  # 进入到网络配置文件目录

# 复制一份刚刚配置的网卡静态ip文件
cp ifcfg-ens33 ifcfg-ens33:1

vim ifcfg-ens33:1
# 修改两个地方
IPADDR=192.168.110.101 修改为 IPADDR=192.168.110.102
DEVICE="ens33"  改为 DEVICE="ens33:1"

esc+:wq  #保存退出

# 重启网卡
systemctl restart network

# 查看是否配置成功
ifconfig
```

