# 表单
和首页一样，我们把表单放到单独的html文件里。什么是表单？请查看W3schools: [英文](https://www.w3schools.com/html/html_forms.asp), [中文](http://www.w3school.com.cn/html/html_forms.asp).

创建register.html:
``` html
<html>
  <header>
    <title>微信机器宝宝</title>
    <style>
    body {
      color: gray;
      text-align: center;
      padding-top: 70px;
    }
    </style>
  </header>
  <body>
    <h1>欢迎使用微信机器宝宝</h1>
    <!-- 点击注册按钮，表单以post的方式，把数据发送给/register-post -->
    <form method="post" action="/register-post">
      邮箱:
      <input type="email" name="email" />
      <br ／>
      用户名:
      <input type="text" name="username" />
      <br ／>
      密码:
      <input type="password" name="password" />
      <br ／><br ／>
      <input type="submit" value="注册">
    </form>
  </body>
</html>
```
更新register.js:
``` js
const http = require('http');
const fs = require('fs');
http.createServer( (req, res) => {
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
通过req.url获取当前的url。如果url是/register，显示注册表单。

### 表单验证
关于表单验证的基础知识，可以查看W3schools: [英文](https://www.w3schools.com/js/js_validation.asp), [中文](http://www.w3school.com.cn/js/js_form_validation.asp).

更新register.html:
``` html
<html>
  <header>
    <title微信机器宝宝</title>
    <style>
    body {
      color: gray;
      text-align: center;
      padding-top: 70px;
    }
    </style>
    <script>
    function validateForm() {
        let email = document.getElementById('email').value;
        let username = document.getElementById('username').value;
        let password = document.getElementById('password').value;
        if (email == '' || username == '' || password == '') {
            alert('请填写所有的注册信息！');
            return false;
        }

        if (password.length < 8) {
          alert('密码长度必须超过8位！');
          return false;
        }
    }
    </script>
  </header>
  <body>
    <h1>欢迎使用微信机器宝宝</h1>
    <!-- 点击注册按钮，表单以post的方式，把表单数据发送给/register -->
    <form method="post" action="/register" onsubmit="return validateForm()">
      邮箱:
      <input type="email" name="email" id="email" />
      <br ／>
      用户名:
      <input type="text" name="username" id="username" />
      <br ／>
      密码:
      <input type="password" name="password" id="password" />
      <br ／><br ／>
      <input type="submit" value="注册">
    </form>
  </body>
</html>
```
给所有需要验证的字段加id属性，然后通过document.getElementById获取对应的字段，对他们的值做验证。这里用到了表单的onsubmit事件。更多关于html事件的知识，请查看W3schools: [英文](https://www.w3schools.com/js/js_htmldom_events.asp), [中文](http://www.w3school.com.cn/js/js_htmldom_events.asp).
