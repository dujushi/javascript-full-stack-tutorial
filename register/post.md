# 获取POST数据
如何获取表单数据呢？

更新register.js
``` js
const http = require('http');
const fs = require('fs');
const queryString = require('querystring');
http.createServer((req, res) => {
  // 如果请求是post方式，获取post数据
  if(req.method == 'POST'){
    let postData = '';

    // 监听请求的data事件，一旦有数据传递就追加到postData里
    req.on('data', data => {
      postData += data;
    });

    // 监听end事件，数据传递结束后，开始处理
    req.on('end', () => {
      console.log(postData);
      // 不同的字段与值用&拼接，值经过url encode处理
      // postData: email=test%40gmail.com&username=test&password=12345678

      const postDataObject = queryString.parse(postData);
      console.log(postDataObject);
      // 借助query string把数据变成对象
      /* postDataObject:
      { email: 'test@gmail.com',
        username: 'test',
        password: '12345678' }
      */

      // 精彩内容留到下一节
      res.end('谢谢提交！');
      return;
    });
  }

  let fileName = 'home.html';
  if (req.url == '/register') {
    fileName = 'register.html';
  }
  fs.readFile(fileName, (err, data) => {
    if (err) throw err;
    res.end(data);
  });
}).listen(8080);
```
