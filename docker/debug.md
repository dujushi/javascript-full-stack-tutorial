# 调试
我们用Chrome开发者工具进行调试。参考: [V8 Inspector Integration for Node.js](https://nodejs.org/dist/latest-v6.x/docs/api/debugger.html#debugger_v8_inspector_integration_for_node_js)

修改Dockerfile:
``` YAML
FROM node:boron-slim
WORKDIR /app
ADD . /app
# 暴露调试端口
EXPOSE 80 9229
RUN npm install -g nodemon
# --inspect
# 打开v8调试
CMD ["nodemon", "--inspect", "server.js"]
```

构建新镜像:
``` sh
# debug是版本号
docker build -t node-hello-world-docker:debug .
```

运行:
``` sh
# -p 9229:9229
# 映射调试端口到本地，这样Chrome才能使用它
docker run -p 4000:80 -p 9229:9229 -v ~/node_hello_world_docker:/app node-hello-world-docker:debug
```

调试：
在Chrome浏览器输入chrome://inspect，就可以发现我们的应用。点击Inspect进入调试页面。点击菜单栏里的Window可以选择要切换的窗口。
