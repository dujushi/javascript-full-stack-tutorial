# 注册链接
我们把第二章做的coming soon页面简单修改一下变成首页。加上注册链接。

创建home.html:
``` html
<html>
  <head>
    <style>
    body {
      color: gray;
      text-align: center;
      padding-top: 70px;
    }
    </style>
  </head>
  <body>
    <h1>Wechat机器宝宝</h1>
    <img src="https://dw9to29mmj727.cloudfront.net/misc/newsletter-naruto3.png" />
    <br />
    <a href="/register">注册</a>
  </body>
</html>
```

创建register.js:
``` js
const http = require('http');
const fs = require('fs');
http.createServer( (req, res) => {
  fs.readFile('home.html', (err, data) => {
    if (err) throw err;
    res.end(data);
  });
}).listen(8080);
```

在本地运行
```
node register.js
```
之后，访问http://localhost:8080 查看首页。我们在首页上添加了一个注册链接。注册链接的url是 [http://localhost:8080/register](http://localhost:8080/register)。Node需要判断一下，如果url是这个的话，就显示注册表单。下一节将实现这个效果。
