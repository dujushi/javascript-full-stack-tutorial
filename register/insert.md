# 保存注册信息
接下来结合第三章的内容，把注册信息保存到数据库里。

更新register.js:
``` js
const http = require('http');
const fs = require('fs');
const queryString = require('querystring');
const uuidV4 = require('uuid/v4');
const mysql = require('mysql');

const dbName = '[database name]';
const dbUsername = '[database username]';
const dbPassword = '[database password]';

http.createServer((req, res) => {
  if(req.method == 'POST'){
    let postData = '';

    req.on('data', data => {
      postData += data;
    });

    req.on('end', () => {
      const postDataObject = queryString.parse(postData);
      const user = {
        username: postDataObject.username,
        password: postDataObject.password,
        email: postDataObject.email,
        verificationCode: uuidV4(),
        status: 0,
        created: Date.now() / 1000
      };
      const connection = mysql.createConnection({
        host     : 'localhost',
        user     : dbUsername,
        password : dbPassword,
        database : dbName
      });
      connection.connect();
      const query = connection.query('INSERT INTO users SET ?', user, (error, results, fields) => {
        if (error) throw error;
        console.log(results);
      });
      connection.end();

      // 精彩内容留到下一节
      res.end('谢谢注册！');
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
}).listen(80);
```
因为需要访问数据库，我们把代码放到Digital Ocean服务器上运行。端口改成80，数据库用户名和密码更新为自己的。
