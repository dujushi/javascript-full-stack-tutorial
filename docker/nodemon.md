# nodemon
使用[nodemon](https://github.com/remy/nodemon)检测代码更新，实时重启应用。

更新Dockerfile:
``` YAML
FROM node:boron-slim
WORKDIR /app
ADD . /app
EXPOSE 80
RUN npm install -g nodemon
CMD ["nodemon", "server.js"]
```

构建新镜像:
``` sh
# nodemon是版本号
docker build -t node-hello-world-docker:nodemon .
```

docker images可以看到新增了一个新版本的镜像。

运行:
``` sh
# -v ~/node_hello_world_docker:/app
# 绑定本地的~/node_hello_world_docker文件夹到容器的/app文件夹
# 更新本地的server.js就等同于更新容器的server.js
# 更多docker run参数： https://docs.docker.com/engine/reference/commandline/run/

# node-hello-world-docker:nodemon指定了使用的镜像版本
docker run -p 4000:80 -v ~/node_hello_world_docker:/app node-hello-world-docker:nodemon
```
此时更新server.js的内容，刷新浏览器可以及时看到更新。
