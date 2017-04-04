# 安装
可以直接在之前创建的DigitalOcean的服务器上安装MySQL。具体可以参考Linode的[这篇博文](https://www.linode.com/docs/databases/mysql/install-mysql-on-ubuntu-14-04)。

登录服务器:
```
ssh root@45.55.165.85
```
更新服务器：
```
sudo apt-get update
```
安装MySQL:
```
sudo apt-get install mysql-server
```
加强MySQL安全配置:
```
sudo mysql_secure_installation
```
登录MySQL:
```
mysql -u root -p
```
查看所有命令:
``` mysql
help;
```
建数据库:
``` mysql
create database wechat;
```
为了安全，我们一般不会直接使用root用户进行操作。所以需要为这个数据库新建一个用户，并授予它操作当前数据库的权限：
``` mysql
grant all on wechat.* to '[username]' identified by '[password]';
```
退出root账号:
``` mysql
exit
```
用新的账号登录:
``` mysql
mysql -u [username] -p
```
指定要操作的数据库:
``` mysql
use wechat
```
创建一个测试表：
``` mysql
create table users
(
  id int NOT NULL AUTO_INCREMENT,
  username varchar(20),
  password varchar(512),
  email varchar(50),
  verificationCode varchar(512),
  status TINYINT(1),
  created int(10) unsigned,
  PRIMARY KEY (id)
);
```
接下来我们练习一下数据库的四个最基本的操作CRUD:

插入一条数据(C: create):
``` mysql
insert into users
(username, password, email, verificationCode, status, created)
values
('admin', 'password', 'test@gmail.com', 'v code', 1, 123456);
```
读取数据(R: read):
``` mysql
select * from users;
```
更新数据(U: update):
``` mysql
update users set password = 'pwd' where id = 1;
```
删除数据(D: delete):
``` mysql
delete from users where id = 1;
```
