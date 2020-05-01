## 安装
```
yum groups install mariadb mariadb-client
systemctl enable mariadb
systemctl start mariadb
```
mysql被oracle收购
sql语句


起服务
mariadb 是 3306 端口



## 修改数据库密码
```
mysql_secure_installation # 帮助管理员配置mariadb,全程yes
```

重启mariadb服务

命令
mysql -q -u root

show database

create database [databasename]创建一个库

use [databasename] 打开数据库

配置文件 users.mdb
添加 Use Contacts(databasename)

mysql -q -u root < users.mdb

show tables

删除库
drop database [databasename]

```
例 查找密码是forsook的用户名是谁 一般查firstname

select * from User_Contacts;

select id from User_Logins where User_Pass = "forsook"

select first_name from User_Names where user_id = 4178
```


创建用户
create user harry@localhost identified by "redhat";
授权
grant select on *.* to harry@localhost;


在命令行操作数据库
```
mysql -u root -e '完整命令'
```

## 备份
### 备份数据库
man mysqldump
备份所有库
mysqldump -u root -p --all-database > /root/mariadb.dump
备份一个库
mysqldump -u root -p --database [databasename] > /root/mariadb.dump

### 恢复备份
* 先创建相应数据库
* 恢复
```
mysql -u root [数据库名] < [].dump
```

查看帮助
rpm -qd mariadb-server
