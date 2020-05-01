## NFS
NFS 传输的是文件系统，主要用于基于文件的存储，实现不同操作系统之间可以共享文件

客户端可以mount，就像使用本地文件系统一样

NFS优点：
1、本地主机使用更少的磁盘空间
2、用户不必在每个主机上创建home目录
3、CDROM

原理：
NFS只提供数据的共享，RPC提供数据传输

yum install nfs-utils rpcbind

主配置文件
```
/ect/exports
```
该配置文件影响配置的目录及其子目录
第一列：共享目录
第二列：允许的主机（参数1 参数2 参数3）通配*


|常用参数
------|------
ro | 只读
rw | 读写
root_squash(默认) | 客户端root权限被映射成nfsnobody用户，如客户端的普通用户是nfsnobody权限，可通过更改others权限控制。
no_root_squash |禁用root_squash
all_squash|(所有用户都映射到匿名用户) 映射到nfsnobody
anonuid==6000|指名映射到6000的用户 anongid
sync|立即存盘，暂时存储在内存内，不直接写盘async



rpcinfo -p localhost


showmount -e server0.example.com

firewall打开
mountd nfs rpc-bind

exportfs -rv 让修改的配置文件生效

默认权限 /var/lib/nfs/etab

重量级配置：（重要）
nfsv4
保护NFS共享的文件访问权限
NFS服务通过多种方法保护文件的访问权限
none:可以对文件进行匿名访问，对服务器的写将分配UID nfsnobody
sys: 标准UID GID 文件权限
krb5 客户端必须使用kerberos证明用户身份，然后再使用Linux文件权限
krb5i 客户端必须使用kerberos证明用户身份，然后再使用Linux文件权限，数据加密
krb5p(考试）客户端必须使用kerberos证明用户身份，然后再使用Linux文件权限，数据加密，保护数据的完整性

*配置的权限*
一看配置文件中写的权限
二看是否root_squansh以及服务器共享目录的权限

在两台主机中 yum authconfig-gtk sssd krb5
/ect/krb5.keytab
只有kerberos用户才可以挂载

/etc/fstab
例 server0.example.com:/public /mnt/data nfs defaults,sec=krb5p 0 0

还可以使用自动挂载autofs

nfs krb5p 挂载测试
必须用ssh先连接ldapuser用户，之后可正常使用su -

### NFS挂载步骤
服务端server0 客户端desktop0
#### 普通挂载
#### 普通挂载服务端
* 启动NFS服务
```
systemctl enable nfs-server
systemctl star  nfs-server
```
* 在服务端创建共享目录，并更改权限
```
mkdir /nornfs
chown nfsnobody /nornfs
```
* 设置共享文件权限。
ro:只读
rw:读写
no_root_squash:
```
vim /etc/exports
/nornfs desktop0(ro) # 配置文件中添加此行，保存退出
exportfs -r
```
* 配置防火墙
```
firewall-cmd --permanent --add-service=nfs
firewall-cmd --reload
```
#### 普通挂载客户端
* 创建挂载点
```
nkdir /mnt/nfs
```
* 挂载，服务端主机名尽量写全
```
vim /etc/fstab
server0:/nornfs /mnt/nfs nfs defauts 0 0 # 保存退出
mount -a
```

### 安全挂载
需要先创建Kerberos用户，这种形式的存储只能由kerberos用户使用
#### 安全挂载服务端
* 安装服务端keytab
```
wget -O /etc/krb5.keytab [].keytab
```
* 修改配置文件
```
vim /etc/sysconfig/nfs
RPCNFSDARGS="-V 4.2"
```
* 启动服务
```
systemctl enable nfs-secure-server
systemctl star nfs-sucure-server
```
* 创建目录
```
mkdir /krbnfs
```
* 修改配置文件,并重新加载
```
echo '/krbnfs desktop0(sec=krb5p,rw)' >> /etc/exports
exportfs -r
```
* 打开防火墙，同普通模式
#### 安全挂载客户端
* 安装客户端keytab
```
wget -O /etc/krb5.keytab [].keytab
```
* 启动服务
```
systemctl enable nfs-secure
systemctl star nfs-sucure
```
* 创建挂载点
```
mkdir /mnt/nfsspace
```
* 新建挂载条目
```
echo 'server0:/krbnfs /mnt/nfsspace nfs defaults,v4.2,sec=krb5p 0 0' >> /etc/fstab
```
* 挂载
```
mount -a
```
## SMB
samba挂载
客户端测试
su - 赋权用户
cifscreds add server0（服务器）
密码(赋权用户密码)
可正常使用
