# 发送验证邮件
如何用Node给用户发送邮件？我们使用npm包: [nodemailer](https://nodemailer.com/about/).

创建email-sample.js:
```js
const nodemailer = require('nodemailer');

const mailerService = '163';
const mailerEmail = '[email address]';
const mailerPassword = '[email password]';
const receiverEmail = '[receiver email address]';
const emailSubject = '微信机器宝宝邮箱验证';
const emailText = 'https://mp.weixin.qq.com/';
const emailHtml = '<a href="https://mp.weixin.qq.com/">https://mp.weixin.qq.com/</a>';

// create reusable transporter object using the default SMTP transport
let transporter = nodemailer.createTransport({
    service: mailerService, // 可以是qq，gmail, 自己搜索其他邮箱提供商的配置
    auth: {
        user: mailerEmail,
        pass: mailerPassword
    }
});

// setup email data with unicode symbols
let mailOptions = {
    from: mailerEmail, // sender address
    to: receiverEmail, // list of receivers
    subject: emailSubject, // Subject line
    text: emailText, // plain text body
    html: emailHtml // html body
};

// send mail with defined transport object
transporter.sendMail(mailOptions, (error, info) => {
    if (error) throw error;
    console.log('Message %s sent: %s', info.messageId, info.response);
});
```
运行：
```
node email-sample.js
```
发送邮件。

整合到register.js:
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

    req.on('data', data => {
      postData += data;
    });

    req.on('end', () => {
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

        // 数据保存成功之后在发送邮件
        let transporter = nodemailer.createTransport({
            service: mailerService,
            auth: {
                user: mailerEmail,
                pass: mailerPassword
            }
        });

        const verificationUrl = `http://${serverIp}/verify/?verificationCode=${verificationCode}`;
        let mailOptions = {
            from: mailerEmail,
            to: postDataObject.email,
            subject: emailSubject,
            text: verificationUrl,
            html: `<a href="${verificationUrl}">${verificationUrl}</a>`
        };

        // send mail with defined transport object
        transporter.sendMail(mailOptions, (error, info) => {
            if (error) throw error;
        });
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
