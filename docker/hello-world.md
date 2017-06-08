# Hello World

### 安装Docker
安装说明: [https://www.docker.com/community-edition](https://www.docker.com/community-edition)

### Node仓库
Node在Docker Hub上有一个[官方仓库](https://hub.docker.com/_/node/)。我们将基于boron-slim搭建环境。

### server.js
创建文件夹: node_hello_world_docker

创建文件: server.js
``` js
const http = require('http');
const server = http.createServer((request, response) => {
  response.writeHead(200, {"Content-Type": "text/plain"});
  response.end("Hello World");
});
server.listen(80);
```
这个文件很简单。创建一个http server，所有访问都直接返回Hello World.

### Dockerfile
Dockerfile用于定义整个运行环境。Dockerfile参考书:[https://docs.docker.com/engine/reference/builder/](https://docs.docker.com/engine/reference/builder/).

创建文件: Dockerfile
``` YAML
# 基于node:boron-slim构建环境
FROM node:boron-slim

# 设置工作目录
WORKDIR /app

# 把当前目录的文件都拷贝到工作目录下
ADD . /app

# 暴露80端口给外部用户
EXPOSE 80

# 运行node server.js命令
CMD ["node", "server.js"]
```

### Image
构建镜像:
``` sh
# node-hello-world-docker 为镜像的名字
docker build -t node-hello-world-docker .
```

查看所有镜像:
``` sh
docker images
```

### 运行
``` sh
# -p 4000:80 表示把本机的4000端口映射到容器的80端口
# 我们就可以通过http://localhost:4000访问网站了
docker run -p 4000:80 node-hello-world-docker
```
在浏览器访问[http://localhost:4000](http://localhost:4000).

你可能已经发现Ctrl + C没有办法关闭当前容器。怎么关闭呢？  

开启一个新的终端, 查看所有容器，获得容器的id:
``` sh
docker ps
```

然后:
```
docker stop [CONTAINER ID]
```

此时你在运行docker ps，就查看不到这个容器了。但是运行docker ps -a的时候还是可以查看到。只是它处于退出状态。

### 参考
[https://nodejs.org/en/docs/guides/nodejs-docker-webapp/](https://nodejs.org/en/docs/guides/nodejs-docker-webapp/)
