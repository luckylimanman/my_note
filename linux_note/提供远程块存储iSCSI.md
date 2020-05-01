IP-SAN ISCSI internet small computer system interafce

```
yum install targetcli
```

targer--->shell 交互

### 配置iSCSI
目标 iqn.2015-07.com.example.com:server0 172.25.0.11
客户端 iqn.2015-07.com.example.com:desktop0 172.25.0.10
#### 服务器
* 进入交互
```
targetcli
```
```
1、创建一个block设备，相当于创建了一个lun。该存储空间vdb1要提前创建
/backstores/block   create  [name]  /dev/vdb1
2、创建一个目标iqn
/iscsi create iqn.2015-
07.com.example.com:server0
3、将1和2关联
/iscsi/iqn.2015-07.com.example.com:server0/tpg1/luns create /backstores/block/[name]
4、创建监听，最后需添加端口号
/iscsi/iqn.2015-07.com.example.com:server0/tpg1/portals create 172.25.0.11 3260
5、创建ACL策略，只有desktop0可以连进该设备
/iscsi/iqn.2015-07.com.example.com:server00/tpg1/acls create iqn.2015-07.com.example.com.redhat:desktop0
6、保存
exit
或 saveconfig 再 exit
```
* 启动服务
```
systemctl enable target.service
systemctl restart target.service
```
* 打开防火墙
```
firewall-cmd --permanent --add-port=3260/tcp
firewall-cmd --reload
```



#### 客户端
* 安装
```
yum install iscsi-initiator-utils
```
修改配置文件
```
vim /etc/iscsi/initiatorname.iscsi
InitiatorName=iqn.2015-07.com.example.com.redhat:desktop0
```
* 发现并登录目标target，以下两条命令都使用server的信息
```
man iscsiadm 找EXAMPLES里面的模版
1、dicover
2、login
```
* 验证iSCSI磁盘的设备节点，如果出现iSCSI新设备则成功，之后可以正常在desktop0上使用该空间
```
iscsiadm -m session -P3 | grep Attached
```


在客户端彻底删除
1、卸载目录
2、模板 logout
2、模板 把 logout 替换成 -o delete


永久挂载时/etc/fstab的写法
defaults,\_netdev 因为是网络设备

例题
