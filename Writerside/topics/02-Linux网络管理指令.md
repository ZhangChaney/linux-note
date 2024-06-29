# 02-Linux网络管理指令

## 1. ifconfig

```bash
ifconfig -a  # 查看所有网卡信息

ifconfig ens33  # ens33是网卡名称

ifconfig {device_name} up/down  # 开启关闭网卡

# 使用ifconfig 添加新的ip地址
ifconfig ens33:1 192.168.110.111 netmask 255.255.255.0 up
# ens33:1  新添加的网卡名称
# 192.168.110.111 新添加的ip地址
# netmask 255.255.255.0 新添加的ip的子网掩码， 默认是就是这个值，可以不写

# 使用ifconfig修改mac地址
ifconfig ens33 hw ether 00:0c:29:fe:ce:20
# ens33 需要修改mac地址的设备名
# hw ether 固定参数
# 00:0c:29:fe:ce:20  新的mac地址
```

## 2. route

```shell
route    # 查看解析过的路由表
route -n  # 查看未解析过的路由表

# 删除网关信息
route del default  # default 指定网关名称
# 添加网关
route add default gw 192.168.110.2  # 网关地址看自己网段
```

## 3. ip

```shell
ip addr show # 查看网络信息  等价于ifconfig
ip link show dev ens33  # 查看ens33的网卡信息  等价于ifconfig ens33
# 加上-s 参数显示收发包数据

# 关闭网卡
ip linke set ens33 down/up  # ens33是网卡名称 down关闭up开启
# 等价于 ifconfig ens33 up/down
```

## 4. netstat

netstat主要用于查询网络端口占用信息

```shell
netstat -a # 查看所有网络端口信息
netstat -t  # 只查看tcp协议
netstat -n  # 查看未解析的信息
netstat -i  # 查看所有网卡的信息
```

## 5. wget

wget指令主要用于下载文件

```shell
wget www.baidu.com
```

## 6. curl

curl指令主要用于发送请求

```shell
curl www.baidu.com
```

