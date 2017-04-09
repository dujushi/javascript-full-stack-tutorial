# 注册后跳转页面
注册成功之后，我们跳转到一个提醒用户验证邮箱的页面。

创建verify-hint.html:
```html
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
    <h1>微信机器宝宝</h1>
    <h2>感谢您注册账号。请前往您的注册邮箱激活账号。</h2>
  </body>
</html>
```
更新register.js:
```js
const http = require('http');
const fs = require('fs');
const queryString = require('querystring');
const uuidV4 = require('uuid/v4');
const mysql = require('mysql');
const nodemailer = require('nodemailer');

const dbName = '[database name]';
const dbUsername = '[database username]';
const dbPassword = '[database password]';

const serverIp = '[digital ocean server ip]';
const mailerService = '163';
const mailerEmail = '[email address]';
const mailerPassword = '[email password]';
const emailSubject = '微信机器宝宝邮箱验证';

http.createServer((req, res) => {
  if(req.method == 'POST'){
    let postData = '';

    req.on('data', function(data){
      postData += data;
    });

    req.on('end', function(){
      const postDataObject = queryString.parse(postData);
      const verificationCode = uuidV4();
      const user = {
        username: postDataObject.username,
        password: postDataObject.password,
        email: postDataObject.email,
        verificationCode: verificationCode,
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
      const query = connection.query('INSERT INTO users SET ?', user, function (error, results, fields) {
        if (error) throw error;

        let transporter = nodemailer.createTransport({
            service: mailerService,
            auth: {
                user: mailerEmail,
                pass: mailerPassword
            }
        });

        const verificationUrl = `http://${serverIp}/verify?verificationCode=${verificationCode}`;
        let mailOptions = {
            from: mailerEmail,
            to: postDataObject.email,
            subject: emailSubject,
            text: verificationUrl,
            html: `<a href="${verificationUrl}">${verificationUrl}</a>`
        };

        transporter.sendMail(mailOptions, (error, info) => {
            if (error) throw error;
        });
      });
      connection.end();

      res.statusCode = 302;
      res.setHeader('Location', '/verify-hint');
      res.end();
    });
  }

  let fileName = 'home.html';
  if (req.url == '/register') {
    fileName = 'register.html';
  } else if (req.url == '/verify-hint') {
    fileName = 'verify-hint.html'
  }
  fs.readFile(fileName, (err, data) => {
    if (err) throw err;
    res.end(data);
  });
}).listen(80);
```
将[status code](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)设成302，浏览器就会自动跳转到location指定的url。
