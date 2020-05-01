#### yum客户端配置目录
/etc/yum.repos.d/
在里面新建配置文件后缀.repo
一个repo库就是一个源
配置文件语法

```
[rhel_dvd] yum源的名字, 只要不与别的源名字重复就行（无规范写法）
gpgcheck = 0 不检查软件包合法性
enabled = 1 启用这个yum源
baseurl = http://content.example.com/rhel7.0/x86_64/dvd 指明yum源地址
name = Remote classroom copy of dvd[root@desktop0 yum.repos.d]# yum源描述信息（无规范写法）
```
