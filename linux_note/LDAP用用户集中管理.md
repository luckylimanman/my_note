用户集中管理


优点
1.允许离线登录，SSSD可以缓存远程服务的用户认证身份
2.避免本地客户端程序对服务器大量连接



> **yum install authconfig**
> authconfig-gtk
> id ldapuserX 查询有没有创建成功
> **yum install autofs 自动挂载**
> ssh连接新用户ldapuser1 密码password(与kerberos结合的密码为kerberos)
>vim /etc/auto.master.d/my.autofs x新建ldap配置文件
该配置文件写法（挂载）
```
/home/guests(家目录去掉最后一部分） /ect/myfile（自定义文件名）
```
命令
vim /etc/myfile
该配置文件写法
```
*    classroom.example.com:/home/guests/&
```
之后重启服务


用户的密码都为password，若使用kerberos，密码都为kerberos
