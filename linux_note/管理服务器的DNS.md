把主机名解析为IP地址

#### DNS解析原理
优先级
cache>/etc/hosts>/etc/resove.conf

查找主机名：
1、递归查询（客户端查询DNS服务器）
 知道 不知道
2、迭代查询（DNS之间交互）
 最佳答案

#### 搭建DNS服务器的正解和反解

yum install unbound
systemctl enable unbound

监听命令：
netstat -ntulp | grep 端口

```
配置文件/etc/unbound/unbound.conf
```
### 配置例，将任何需求转发到classroom.example.com
* /etc/unbound/unbound.conf 配置文件里
```
interface 本机地址 #本机对外提供服务的IP地址，与其在同一个网段可以访问
access-control: 0.0.0.0/0 allow
domain-insecure: "example.com" #取消其注释，免于DNSSEC认证
forward-zone:
        name: .
        forward-host(forward-addr): classroom.examole.com
```
* 检查语法
```
unbound-checkconf
```
* 重启服务
* 防火墙打开dns服务
* 检查是否设置成功查看A记录







解析
dig + 主机名
