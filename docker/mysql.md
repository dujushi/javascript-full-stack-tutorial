# MySQL
我们将直接使用Docker Hub上的[MySQL官方包](https://hub.docker.com/_/mysql/)。

打开终端，运行:
``` sh
# -d 后台运行该容器
# -v ~/node_mysql_docker/data:/var/lib/mysql
#   /var/lib/mysql是MySQL保存数据的目录
#   此命令将/var/lib/mysql的数据映射到本机的data目录。之后杀死容器，数据库数据仍然可以在本机找到
# -e MYSQL_ROOT_PASSWORD=password 设置root用户密码。
# mysql 是镜像的名字，默认用latest版本
# --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci运行mysqld的参数，使用utf8字符集，支持中文
docker run -d -v ~/node_mysql_docker/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=password mysql --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci
```

如何操作数据库? 先登录容器:
``` sh
docker exec -it [CONTAINER ID] /bin/bash
```

之后登录数据库:
``` sh
mysql -u root -p
```
