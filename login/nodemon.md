# nodemon
当我们使用node运行代码的时候，每次更新完代码，都需要重新运行node命令，更新才会被加载。这样非常麻烦。[nodemon](https://nodemon.io/)会检测文件更新，并且自动重新加载更新。

安装nodemon:
```
npm install -g nodemon
```
创建nodemon-sample.js:
```js
const http = require('http');
http.createServer((req, res) => {
  res.end('Thanks nodemon!');
}).listen(8080);
```
运行程序:
```
nodemon nodemon-sample.js
```
随意改动一下nodemon-sample.js，nodemon会马上检测到更新。终端上会出现类似的log:
```
[nodemon] restarting due to changes...
[nodemon] starting `node nodemon-sample.js`
```
此时刷新浏览器，可以立即看到更新。
