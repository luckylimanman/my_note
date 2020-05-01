在rhel7网络控制，NetworkManager
systemctl restart NetworkManager --->自动检测网络，自动连接网络的程序，无论是无线还是有线网络
rhel6 NetworkManager--->不支持很多高级网络配置
网卡别名
网卡绑定
网卡桥接
在rhel7中都可以使用

TCP/IP
应用层 传输层 网络层 链路层

### nmcli
非常强大的命令，主要使用nmcli配置网络，配置桥接，配置网卡绑定

>IPV4 32位

类型 | IP地址 | 子网掩码 | 网络位/主机位 |私有地址
---|---------|---------|-----------|---------
A | 0.0.0.0-126.255.255.0| 255.0.0.0| 网络位（8）+主机位（24）|10.0.0.0=10.255.255.255
B |128.0.0.0-191.255.255.255| 255.255.0.0 |网络位（16）+主机位（16）|172.16.0.0-172.31.255.255
C |192.0.0.0-233.255.255.255| 255.255.255.0| 网络位（24）+主机位（8）|192.168.0.0-192.168.255.255
D |组播地址 224=239|
E |保留（科学研究）240-254|
私有地址在局域网使用
特殊地址
169.254.0/0 dhcp失败
127.0.0.0/8 本地回环地址
出现问题排错顺序
ping 127.0.0.1 ping ip ping gateway




>IPV6

OSI 应用层 会话层 传输层 网络层 链路层 物理层

TCP/IP 应用层 传输层 网络层 链路层 物理层
网络设备：
交换机--->链路层 二层
路由器--->网络层 三层

路由表--->不只有路由器有路由表，主机相当于一个功能简单的路由器，也有路由表


直连路由 由IP地址和网络位决定，优先级最高
默认路由 所有路由不匹配，使用默认路由
动态路由 rip ospf eigrp


查看路由表(补充贴上路由表）
route -n
默认路由 UG
直连路由 U
到主机路由 UGH


以前网卡的命名：eth0 eth1 eth2 eth3 可以通过配置使用旧名称
现在根据固件，拓扑分配网卡名称
enp3s0
en:ethernet以太网 wl：无线网
p:pci
o:板载
s:热插拔
数字N：代表索引、端口

BIOS

若虚机 rhe7，则eth0 因为是硬件是虚拟出来的



查看网络层IP地址
ifconfig 查看当前激活网卡
ifconfig -a 查看所有网络
ifconfig 网卡名称
ip addr show
ip addr show 网卡名称

查看链路层状态
ip link show
ip link show 网卡名称


**配置网络**
使用networkmanager服务
网卡配置文件所在目录
/etc/sysconfig/network-scripts
网卡配置文件 ifcfg-eth0
```bash
[root@desktop0 network-scripts]# cat ifcfg-eth0
DEVICE=eth0 # 设备名字
BOOTPROTO=dhcp #（none静态/dhcp动态/static静态）
ONBOOT=yes 该网卡在系统启动时自动激活
TYPE=Ethernet 网卡类型
USERCTL=yes 允许非root用户控制设备
PEERDNS=yes 使用DNS选项替代/etc/resove.conf,“NO”不更改该配置
IPV6INIT=no
PERSISTENT_DHCLIENT=1
```

查看NetworkManager服务运行状态
若使用nmcli NetworkManager必须运行

nmcli
network manager command line

nmcli 可以跟很多子命令

nmcli connection show 查看所有存在的网络配置文件
nmcli connection show

**把网络修改成静态**
例
修改为以下配置
IPAddr：172.25.X.10
NETMASK：255.255.255.0
GATEWAY：172.25.X.254
DNS:172.25.254.254
HOSTNAME=serverX.example.com

nmcli connection midify 'System eth0' ipv4.addresses '172.25.0.10/24 172.25.0.254' ipv4.dns 172.25.254.254 ipv4.method mannual
nmcli connection up 'System eth0'
hostnamectl set-hostname  desktop0.example.com

一个网卡可以配多个ip地址
添加IP
nmcli connection modify mytesteth0 +ipv4.addresses 10.10.10.10/16
添加dns
[root@desktop0 ~]# nmcli connection modify 'System eth0' +ipv4.dns 8.8.8.8
[root@desktop0 ~]# nmcli connection up 'System eth0'
查看dns配置文件 cat /etc/resolv.conf
**添加连接(connection)**
例
创建
nmcli connection add con-name 'mytesteth0' type ethernet ifname eth0
激活
nmcli connection modify mytesteth0 ipv4.addresses '172.25.0.110/24 172.25.0.254' ipv4.dns 172.25.254.254 ipv4.method manual


图形化修改ip
nmtui

**查看路由**
route -n
**添加路由**
查看路由表，纵向对齐 ip route show | column -t
配置路由
暂时生效,重启系统后失效
ip route add 30.0.0.0/24 via 172.25.0.10 dev eth0
永久生效
[root@desktop0 ~]# vim /etc/sysconfig/network-scripts/route-eth0
配置文件内语法
10.10.10.0/24 via 172.25.0.254 dev eth0
20.10.10.0/24 via 172.25.0.254 dev eth0
[root@desktop0 ~]# systemctl restart NetworkManager
route -n 查看是否添加

**查看网卡状态**
ethtool

**更改主机名**
hostnamectl 永久更改
hostname 临时更改
动态分配的主机名 locahost@localdomian

**配置解析文件**
/ect/hosts
语法
```
ip地址 域名 （别名）
```

临时传输文件
nc
