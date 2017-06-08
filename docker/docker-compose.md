# Docker Compose
[Docker Compose](https://docs.docker.com/compose/)可以定义多个Container如何同时运行。本节我们将用它来同时管理Node和MySQL容器。

创建文件夹: nodejs-mysql-docker

在它下面创建文件docker-compose.yml:
``` YAML
# https://docs.docker.com/compose/compose-file/
version: "3"
services:
  web:
    build: ./nodejs-docker
    ports:
      - "8080:80"
      - "9229:9229"
    volumes:
      - ./nodejs-docker:/app
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

在nodejs-mysql-docker下，创建文件夹: nodejs-docker. 把node_hello_world_docker下的文件复制过来。

运行:
``` sh
docker-compose up
```
再运行docker ps，你将看到docker-compose创建的两个容器。

想在nodejs-docker里访问MySQL容器?
在nodejs-docker文件夹下添加package.json:
``` json
{
  "name": "nodejs-docker",
  "version": "1.0.0",
  "description": "",
  "main": "server.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "dependencies": {
    "mysql": "^2.13.0"
  }
}
```
运行:
``` sh
npm install
```
安装npm包。

更新server.js:
``` js
const http = require('http');
const server = http.createServer((request, response) => {
  const mysql = require('mysql');
  const connection = mysql.createConnection({
    host: 'mysql',
    user: 'root',
    password: 'password',
    database: 'mysql'
  });
  connection.connect();
  connection.query('select User from user limit 1', (error, results, fields) => {
    if (error) throw error;
    response.writeHead(200, {"Content-Type": "text/plain"});
    response.end("MySQL User: " + results[0].User);
  });
  connection.end();
});
server.listen(80);
```
刷新页面，即可看到从数据库读取的一个MySQL用户名。

以上都是为开发环境服务。如果是产品环境的话，我们不希望运行nodemon，也不希望打开调试模式。我们还希望使用pm2保证应用挂了之后，可以自动重启。可以新建一个专门供产品环境使用的Dockerfile。

在nodejs-docker下，新建Dockerfile-prod:
``` YAML
FROM node:boron-slim
WORKDIR /app
ADD . /app
# 去除9229调试端口
EXPOSE 80
# 不需要安装nodemon，安装pm2
RUN npm install -g pm2
# 安装npm包
# 开发环境需要在主机上手工跑。因为开发环境使用了volumns参数。即使安装了，也会被覆盖。可以自己试验一下。
RUN npm install
# 用pm2-docker启动应用
# http://pm2.keymetrics.io/docs/usage/docker-pm2-nodejs/
CMD ["pm2-docker", "server.js"]
```

在nodejs-docker下，新建.dockerignore文件:
```
# 新建镜像的时候，忽略node_modules目录
node_modules
```

新建docker-compose-prod.yml:
``` yml
version: "3"
services:
  web:
    build:
      context: ./nodejs-docker
      # 指定使用产品环境的Dockfile
      dockerfile: Dockerfile-prod
    ports:
      # 去除9229端口
      - "80:80"
    # 去除volumes, 这个主要是为实时更新容器的代码。产品环境不需要。
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

因为更新了Dockerfile，需要重新build:
``` sh
# -f docker-compose-prod.yml 指定使用的compose文件
docker-compose -f docker-compose-prod.yml build
```

可以运行了:
``` sh
docker-compose -f docker-compose-prod.yml up
```
