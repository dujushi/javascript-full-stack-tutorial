# 服务器输出html
其实我们只需要把第一章里面的Hello World改成html就可以了。

创建coming-soon.js:
``` js
const http = require('http');
http.createServer( (req, res) => {
  res.end('<html><body><h1>Coming Soon ...</h1></body></html>');
}).listen(8080);
```
因为Mac默认不允许使用80端口。所以本地开发的时候，我们只能使用8080端口。

在终端执行:
```
node coming-soon.js
```
然后就可以通过浏览器访问了:http://localhost:8080/

也可以使用file system模块的函数，读取coming-soon.html的内容，输出。

修改coming-soon.js：
``` js
const http = require('http');
const fs = require('fs');
http.createServer( (req, res) => {
  fs.readFile('coming-soon.html', (err, data) => {
    if (err) throw err;
    res.end(data);
  });
}).listen(8080);
```
点击查看[readFile的文档](https://nodejs.org/dist/latest-v6.x/docs/api/fs.html#fs_fs_readfile_file_options_callback).
