# login.js
先更新home.html添加登录链接:
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
    <img src="https://dw9to29mmj727.cloudfront.net/misc/newsletter-naruto3.png" />
    <br />
    <a href="/register">注册</a>
    <a href="/login">登录</a>
  </body>
</html>
```

添加登录表单login.html，并添加简单的表单验证，提醒用户输入用户名和密码再提交表单:
```html
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
    <script>
    function validateForm() {
        let email = document.getElementById('email').value;
        let password = document.getElementById('password').value;
        if (email == '' || password == '') {
            alert('请输入邮箱和密码！');
            return false;
        }
    }
    </script>
  </header>
  <body>
    <h1>欢迎使用微信机器宝宝</h1>
    <form method="post" action="/login" onsubmit="return validateForm()">
      邮箱:
      <input type="text" name="email" id="email" />
      <br ／>
      密码:
      <input type="password" name="password" id="password" />
      <br ／><br ／>
      <input type="submit" value="登录">
    </form>
  </body>
</html>
```

添加login.js，让它可以正常的显示首页和登录页。等完成登录功能之后，我们再合并login.js和register.js，否则代码太多了，看了头痛。
```js
const http = require('http');
const url = require('url');
const fs = require('fs');

http.createServer((req, res) => {
  let currentUrl = url.parse(req.url, true);
  let fileName = 'home.html';
  if (currentUrl.pathname == '/login') {
    fileName = 'login.html';
  }
  fs.readFile(fileName, (err, data) => {
    if (err) throw err;
    res.end(data);
  });
}).listen(8080);
```

更新login.js，生成session id:
```js
const http = require('http');
const url = require('url');
const fs = require('fs');
const cookie = require('cookie');
const uuidV4 = require('uuid/v4');

http.createServer((req, res) => {
  let currentUrl = url.parse(req.url, true);

  let cookies = cookie.parse(req.headers.cookie || '');
  let sessionId = cookies.wechatbaby || '';
  if (sessionId == '') {
    let sessionId = uuidV4();
    let sessionIdCookie = cookie.serialize('wechatbaby', sessionId, {
        maxAge: 60 * 60 * 24
    });
    res.setHeader('Set-Cookie', [sessionIdCookie]);
  }

  let fileName = 'home.html';
  if (currentUrl.pathname == '/login') {
    fileName = 'login.html';
  }
  fs.readFile(fileName, (err, data) => {
    if (err) throw err;
    res.end(data);
  });
}).listen(8080);
```

更新login.js, 获取提交的表单数据:
```js
const http = require('http');
const url = require('url');
const fs = require('fs');
const cookie = require('cookie');
const uuidV4 = require('uuid/v4');
const queryString = require('querystring');

http.createServer((req, res) => {
  let currentUrl = url.parse(req.url, true);

  let cookies = cookie.parse(req.headers.cookie || '');
  let sessionId = cookies.wechatbaby || '';
  if (sessionId == '') {
    let sessionId = uuidV4();
    let sessionIdCookie = cookie.serialize('wechatbaby', sessionId, {
        maxAge: 60 * 60 * 24
    });
    res.setHeader('Set-Cookie', [sessionIdCookie]);
  }

  if (req.method == 'POST') {
    let postData = '';

    req.on('data', data => {
      postData += data;
    });

    req.on('end', () => {
      const postDataObject = queryString.parse(postData);
      // { email: 'test@gmail.com', password: '12345678' }
      console.log(postDataObject);
      res.end('Thanks!');
    });
    return;
  }

  let fileName = 'home.html';
  if (currentUrl.pathname == '/login') {
    fileName = 'login.html';
  }
  fs.readFile(fileName, (err, data) => {
    if (err) throw err;
    res.end(data);
  });
}).listen(8080);
```

更新login.js，查询数据库判断邮箱和密码是否正确。因为数据库在Digital Ocean的服务器上，所以我们把端口改成80，将代码移到服务器上执行。
```js
const http = require('http');
const url = require('url');
const fs = require('fs');
const cookie = require('cookie');
const uuidV4 = require('uuid/v4');
const queryString = require('querystring');
const mysql = require('mysql');

const dbName = '[database name]';
const dbUsername = '[database username]';
const dbPassword = '[database password]';

http.createServer((req, res) => {
  let currentUrl = url.parse(req.url, true);

  let cookies = cookie.parse(req.headers.cookie || '');
  let sessionId = cookies.wechatbaby || '';
  if (sessionId == '') {
    let sessionId = uuidV4();
    let sessionIdCookie = cookie.serialize('wechatbaby', sessionId, {
        maxAge: 60 * 60 * 24
    });
    res.setHeader('Set-Cookie', [sessionIdCookie]);
  }

  if (req.method == 'POST') {
    let postData = '';

    req.on('data', data => {
      postData += data;
    });

    req.on('end', () => {
      const postDataObject = queryString.parse(postData);

      if (currentUrl.pathname == '/login') {
        const connection = mysql.createConnection({
          host     : 'localhost',
          user     : dbUsername,
          password : dbPassword,
          database : dbName
        });
        connection.connect();
        const query = connection.query('SELECT * FROM users WHERE email = ? and password = ?', [postDataObject.email, postDataObject.password], (error, results, fields) => {
          connection.end();

          if (error) throw error;

          if (results.length > 0) {
            res.end(`Hi, ${results[0].username}`);
          } else {
            res.end('用户名和密码错误！');
          }
        });
      }
    });
    return;
  }

  let fileName = 'home.html';
  if (currentUrl.pathname == '/login') {
    fileName = 'login.html';
  }
  fs.readFile(fileName, (err, data) => {
    if (err) throw err;
    res.end(data);
  });
}).listen(80);
```
更新login.js, 将用户id和用户名保存到sessions里：
```js
const http = require('http');
const url = require('url');
const fs = require('fs');
const cookie = require('cookie');
const uuidV4 = require('uuid/v4');
const queryString = require('querystring');
const mysql = require('mysql');

let sessions = [];

const dbName = '[database name]';
const dbUsername = '[database username]';
const dbPassword = '[database password]';

http.createServer((req, res) => {
  let currentUrl = url.parse(req.url, true);

  let cookies = cookie.parse(req.headers.cookie || '');
  let sessionId = cookies.wechatbaby || '';
  if (sessionId == '') {
    let sessionId = uuidV4();
    let sessionIdCookie = cookie.serialize('wechatbaby', sessionId, {
        maxAge: 60 * 60 * 24
    });
    res.setHeader('Set-Cookie', [sessionIdCookie]);
  }

  if (req.method == 'POST') {
    let postData = '';

    req.on('data', data => {
      postData += data;
    });

    req.on('end', () => {
      const postDataObject = queryString.parse(postData);

      if (currentUrl.pathname == '/login') {
        const connection = mysql.createConnection({
          host     : 'localhost',
          user     : dbUsername,
          password : dbPassword,
          database : dbName
        });
        connection.connect();
        const query = connection.query('SELECT * FROM users WHERE email = ? and password = ?', [postDataObject.email, postDataObject.password], (error, results, fields) => {
          connection.end();

          if (error) throw error;

          if (results.length > 0) {
            let session = {
              username: results[0].username,
              id: results[0].id
            };
            sessions[sessionId] = session;
            /*
            [ 'c4f0768d-b96d-43fe-b7a7-362ecf8a1914': { username: 'test', id: 3 } ]
            */
            console.log(sessions);
            res.end(`Hi, ${results[0].username}`);
          } else {
            res.end('用户名和密码错误！');
          }
        });
      }
    });
    return;
  }

  let fileName = 'home.html';
  if (currentUrl.pathname == '/login') {
    fileName = 'login.html';
  }
  fs.readFile(fileName, (err, data) => {
    if (err) throw err;
    res.end(data);
  });
}).listen(80);
```
更新login.js, 登录之后跳转到profile页面:
```js
const http = require('http');
const url = require('url');
const fs = require('fs');
const cookie = require('cookie');
const uuidV4 = require('uuid/v4');
const queryString = require('querystring');
const mysql = require('mysql');

let sessions = [];

const dbName = '[database name]';
const dbUsername = '[database username]';
const dbPassword = '[database password]';

http.createServer((req, res) => {
  let currentUrl = url.parse(req.url, true);

  let cookies = cookie.parse(req.headers.cookie || '');
  let sessionId = cookies.wechatbaby || '';
  if (sessionId == '') {
    let sessionId = uuidV4();
    let sessionIdCookie = cookie.serialize('wechatbaby', sessionId, {
        maxAge: 60 * 60 * 24
    });
    res.setHeader('Set-Cookie', [sessionIdCookie]);
  }

  // 获得当前用户的session
  let session = null;
  if (sessionId in sessions) {
    session = sessions[sessionId];
  }

  if (req.method == 'POST') {
    let postData = '';

    req.on('data', data => {
      postData += data;
    });

    req.on('end', () => {
      const postDataObject = queryString.parse(postData);

      if (currentUrl.pathname == '/login') {
        const connection = mysql.createConnection({
          host     : 'localhost',
          user     : dbUsername,
          password : dbPassword,
          database : dbName
        });
        connection.connect();
        const query = connection.query('SELECT * FROM users WHERE email = ? and password = ?', [postDataObject.email, postDataObject.password], (error, results, fields) => {
          connection.end();

          if (error) throw error;

          if (results.length > 0) {
            let session = {
              username: results[0].username,
              id: results[0].id
            };
            sessions[sessionId] = session;

            // 复习第四章：如何跳转页面
            res.statusCode = 302;
            res.setHeader('Location', '/profile');
            res.end();
          } else {
            res.end('用户名和密码错误！');
          }
        });
      }
    });
    return;
  }

  if (currentUrl.pathname == '/profile') {
    // session为null，说明没有登录过，跳转到登录页面
    if (session == null) {
      res.statusCode = 302;
      res.setHeader('Location', '/login');
      res.end();
    } else {
      res.end(`
        <html>
          <body>
            <h1>你好，${session.username}</h1>
          </body>
        </html>
      `);
    }
    return;
  }

  let fileName = 'home.html';
  if (currentUrl.pathname == '/login') {
    fileName = 'login.html';
  }
  fs.readFile(fileName, (err, data) => {
    if (err) throw err;
    res.end(data);
  });
}).listen(80);
```
更新login.js，添加退出按钮:
```js
const http = require('http');
const url = require('url');
const fs = require('fs');
const cookie = require('cookie');
const uuidV4 = require('uuid/v4');
const queryString = require('querystring');
const mysql = require('mysql');

let sessions = [];

const dbName = '[database name]';
const dbUsername = '[database username]';
const dbPassword = '[database password]';

http.createServer((req, res) => {
  let currentUrl = url.parse(req.url, true);

  let cookies = cookie.parse(req.headers.cookie || '');
  let sessionId = cookies.wechatbaby || '';
  if (sessionId == '') {
    let sessionId = uuidV4();
    let sessionIdCookie = cookie.serialize('wechatbaby', sessionId, {
        maxAge: 60 * 60 * 24
    });
    res.setHeader('Set-Cookie', [sessionIdCookie]);
  }

  let session = null;
  if (sessionId in sessions) {
    session = sessions[sessionId];
  }

  if (req.method == 'POST') {
    let postData = '';

    req.on('data', data => {
      postData += data;
    });

    req.on('end', () => {
      const postDataObject = queryString.parse(postData);

      if (currentUrl.pathname == '/login') {
        const connection = mysql.createConnection({
          host     : 'localhost',
          user     : dbUsername,
          password : dbPassword,
          database : dbName
        });
        connection.connect();
        const query = connection.query('SELECT * FROM users WHERE email = ? and password = ?', [postDataObject.email, postDataObject.password], (error, results, fields) => {
          connection.end();

          if (error) throw error;

          if (results.length > 0) {
            let session = {
              username: results[0].username,
              id: results[0].id
            };
            sessions[sessionId] = session;

            res.statusCode = 302;
            res.setHeader('Location', '/profile');
            res.end();
          } else {
            res.end('用户名和密码错误！');
          }
        });
      }
    });
    return;
  }

  if (currentUrl.pathname == '/profile') {
    if (session == null) {
      res.statusCode = 302;
      res.setHeader('Location', '/login');
      res.end();
    } else {
      res.end(`
        <html>
          <body>
            <h1>你好，${session.username}</h1>
            <a href="/logout">退出</a>
          </body>
        </html>
      `);
    }
    return;
  }

  if (currentUrl.pathname == '/logout') {
    // 删除sessions里对应的session
    // 详见如何删除关联数组的元素: https://www.w3schools.com/js/js_array_methods.asp
    delete sessions[sessionId];
    res.statusCode = 302;
    res.setHeader('Location', '/login');
    res.end();
    return;
  }

  let fileName = 'home.html';
  if (currentUrl.pathname == '/login') {
    fileName = 'login.html';
  }
  fs.readFile(fileName, (err, data) => {
    if (err) throw err;
    res.end(data);
  });
}).listen(80);
```
因为sessions是login.js的一个变量。一旦停止程序，sessions就消失了。所以当我们修改文件，重新运行Node的时候，之前的登录消息就不见了。 如果要解决这个问题，需要把sessions保存到数据库里。

我们访问/logout，它会删除session。此时，我们再去访问/profile的时候，session就是null，它会跳转到登录页面，要求用户登录。
