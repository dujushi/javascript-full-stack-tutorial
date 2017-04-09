# 激活账号
从url中获取验证码，查询验证码是否存在。不存在的话，提示错误消息。存在的话，更新用户状态，删除验证码。

### 获取验证码
url的组成如下:
```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                    href                                     │
├──────────┬┬───────────┬─────────────────┬───────────────────────────┬───────┤
│ protocol ││   auth    │      host       │           path            │ hash  │
│          ││           ├──────────┬──────┼──────────┬────────────────┤       │
│          ││           │ hostname │ port │ pathname │     search     │       │
│          ││           │          │      │          ├─┬──────────────┤       │
│          ││           │          │      │          │ │    query     │       │
"  http:   // user:pass @ host.com : 8080   /p/a/t/h  ?  query=string   #hash "
│          ││           │          │      │          │ │              │       │
└──────────┴┴───────────┴──────────┴──────┴──────────┴─┴──────────────┴───────┘
(all spaces in the "" line should be ignored -- they are purely for formatting)
```
激活账号的url是/verify?verificationCode=12345. 我们需要解析这个url，获取pathname和query部分。我们通过[url](https://nodejs.org/dist/latest-v6.x/docs/api/url.html)模块来实现。

创建url-sample.js:
```js
const http = require('http');
const url = require('url');
http.createServer((req, res) => {
  let currentUrl = url.parse(req.url, true);
  console.log(currentUrl);
  res.end('url sample');
}).listen(8080);
```
访问[http://localhost:8080/verify?verificationCode=12345](http://localhost:8080/verify?verificationCode=12345). currentUrl的结果如下:
```
Url {
  protocol: null,
  slashes: null,
  auth: null,
  host: null,
  port: null,
  hostname: null,
  hash: null,
  search: '?verificationCode=12345',
  query: { verificationCode: '12345' },
  pathname: '/verify',
  path: '/verify?verificationCode=12345',
  href: '/verify?verificationCode=12345' }
```

### 完成激活账号
更新register.js:
```js
const http = require('http');
const fs = require('fs');
const queryString = require('querystring');
const uuidV4 = require('uuid/v4');
const mysql = require('mysql');
const nodemailer = require('nodemailer');
const url = require('url');

const dbName = '[database name]';
const dbUsername = '[database username]';
const dbPassword = '[database password]';

const serverIp = '[digital ocean server ip]';
const mailerService = '163';
const mailerEmail = '[email address]';
const mailerPassword = '[email password]';
const emailSubject = '微信机器宝宝邮箱验证';

http.createServer((req, res) => {
  // 获取当前url信息
  let currentUrl = url.parse(req.url, true);

  if(req.method == 'POST'){
    let postData = '';

    req.on('data', data => {
      postData += data;
    });

    req.on('end', () => {
      const postDataObject = queryString.parse(postData);
      if (currentUrl.pathname == '/register') {
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
        const query = connection.query('INSERT INTO users SET ?', user, (error, results, fields) => {
          connection.end();

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

        res.statusCode = 302;
        res.setHeader('Location', '/verify-hint');
        res.end();
      }
    });
  }

  if (currentUrl.pathname == '/verify') {
    const verificationCode = currentUrl.query.verificationCode;
    // 每次http请求需要重新连数据库
    const connection = mysql.createConnection({
      host     : 'localhost',
      user     : dbUsername,
      password : dbPassword,
      database : dbName
    });
    connection.connect();
    connection.query('SELECT * FROM users WHERE verificationCode = ?', verificationCode, (error, results, fields) => {
      if (error) {
        // 发生错误，结束数据库连接
        connection.end();
        throw error;
      }
      if (results.length == 0) {
        res.end('验证码已失效！请重新注册！');
      } else {
        connection.query('UPDATE users SET status = ?, verificationCode = ? WHERE id = ?', [1, '', results[0].id], (error, results, fields) => {
          // 最后一个数据库请求，总是关闭数据库连接
          connection.end();
          if (error) throw error;
          res.end('恭喜您注册成功！');
        });
      }
    });
    // return: 阻止程序继续运行后面的代码
    return;
  }

  let fileName = 'home.html';
  if (currentUrl.pathname == '/register') {
    fileName = 'register.html';
  } else if (currentUrl.pathname == '/verify-hint') {
    fileName = 'verify-hint.html'
  }
  fs.readFile(fileName, (err, data) => {
    if (err) throw err;
    res.end(data);
  });
}).listen(80);
```
