openssh 在Linux系统下开源的协议，开放且免费

## 从客户端 ssh提供两种安全验证
### 1、基于用户名和口令安全验证
   首先客户端向服务器发送一个请求
   第一次连接服务器，服务器向客户端发送一个公钥，如果接受后保存在～/.ssh/known)hosts
   客户端使用服务器传送过来的公钥对用户密码进行加密，并且发送给服务器
   服务器接受公钥加密的密码后，首先利用私钥进行解密，检查客户端发送过来的密码是否正确/etc/shadow
   当密码接受成功后，开始建立通信
   （以上过程基于密钥的安全验证也有）
### 2、基于密钥安全验证




只有用过密钥安全验证才可以做自动化

```
客户端配置文件
ssh_config
服务端配置文件
sshd_config
```
加速ssh
UseDNS no
GSSAPIAuthentication no


通过ssh-copy-id复制饿密钥，复制到服务器的
～/.ssh/authorized_key 里
