# 发布
我们完成项目之后，如何发布到Digital Ocean呢？我们可以使用Docker Machine很轻松的完成这个工作。

参考: [https://docs.docker.com/machine/examples/ocean/](https://docs.docker.com/machine/examples/ocean/)

按照参考文章，得到access token之后，我们可以使用Docker Machine命令完成所有的操作。

新建服务器:
``` sh
docker-machine create --driver digitalocean --digitalocean-access-token [access token] nodejs-mysql-docker
```

查看所有服务器:
``` sh
docker-machine ls
```

清理本地用来保存MySQL数据的data目录以及node_modules目录:
``` sh
rm -rf data
rm -rf nodejs-docker/node_modules
```

更新docker-compose-prod.yml:
``` sh
version: "3"
services:
  web:
    build:
      context: ./nodejs-docker
      dockerfile: Dockerfile-prod
    ports:
      # 我们希望直接通过IP访问网页
      - "80:80"
    networks:
      - webnet
  mysql:
    image: mysql
    command: mysqld --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci
    environment:
      - MYSQL_ROOT_PASSWORD=password
    ports:
      - "3306:3306"
    volumes:
      - ./data:/var/lib/mysql
    networks:
      - webnet
networks:
  webnet:
```

上传本地文件到服务器:
``` sh
docker-machine scp -r . nodejs-mysql-docker:/app
```

运行应用:
``` sh
# 更新环境变量，使得之后的docker-compose命令可以操作服务器
eval $(docker-machine env nodejs-mysql-docker)

# -d 实现后台运行
docker-compose -f docker-compose-prod.yml up -d
```

获得服务器IP:
``` sh
docker-machine ip nodejs-mysql-docker
```
这样就可以直接通过服务器IP访问网站了。

远程访问服务器:
``` sh
docker-machine ssh nodejs-mysql-docker
```

删除机器:
``` sh
docker-machine rm nodejs-mysql-docker
```
