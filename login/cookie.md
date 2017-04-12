# Cookie
我们可以在服务器端设置cookie，也可以在浏览器端设置cookie.

创建cookie-sample.js:
```js
const http = require('http');
http.createServer((req, res) => {
  // 在服务器端， 可以通过req.headers.cookie查看浏览器发送过来的cookie
  console.log(req.headers.cookie);
  // 通过set cookie添加新的cookie
  // 包括过期日期，和有效路径
  res.setHeader('Set-Cookie', [
    'key1=value1; expires=Thu, 18 Dec 2017 12:00:00 UTC; path=/',
    'key2=value2; expires=Thu, 18 Dec 2017 12:00:00 UTC; path=/'
  ]);
  res.end('Hello Cookie!');
}).listen(8080);
```
运行:
```
nodemon cookie-sample.js
```
在Chrome上打开一个新的tab，按option + command + i启动开发者工具，查看network tab. 访问[http://localhost:8080](http://localhost:8080)。你会看到有两个请求：一个是localhost的请求，另一个是favicon的请求。favicon是浏览器默认的请求。它是网站的小图标，用于显示在浏览器的标签处。点击localhost请求查看Headers。Response Headers有两个set cookie记录，是服务器发送过来的更新cookie的命令:
```
Set-Cookie:key1=value1; expires=Thu, 18 Dec 2017 12:00:00 UTC; path=/
Set-Cookie:key2=value2; expires=Thu, 18 Dec 2017 12:00:00 UTC; path=/
```
第一次请求的时候，Request Headers里面的Cookie是空的。因为访问了页面之后，服务器才告诉浏览器要设置cookie. 随后的请求， Cookie的值如下:
```
Cookie:key1=value1; key2=value2
```
此处，要注意更新你的过期日期，确保是未来时间。

查看终端的log，也可以看到类似的输出:
```
key1=value1; key2=value2
```
浏览器会一直保留这些cookie，直到过期。

### 删除cookie
浏览器端可以删除cookie。在Chrome开发者工具端的console里实验一下：
```
document.cookie = "key1=; expires=Thu, 18 Dec 2013 12:00:00 UTC; path=/";
```
把cookie的值设置为空，把有效日期设置为过去时间，就可以删除cookie了。再次请求网页的时候，request headers里面的cookie只剩下key2。

服务器端也可以删除cookie。更新cookie-sample.js:
```js
const http = require('http');
http.createServer((req, res) => {
  // 在服务器端， 可以通过req.headers.cookie查看浏览器发送过来的cookie
  console.log(req.headers.cookie);
  // 删除cookie
  res.setHeader('Set-Cookie', [
    'key1=; expires=Thu, 18 Dec 2013 12:00:00 UTC; path=/',
    'key2=; expires=Thu, 18 Dec 2013 12:00:00 UTC; path=/'
  ]);
  res.end('Hello Cookie!');
}).listen(8080);
```
刷新一次页面删除cookie，再刷新一次页面，查看request headers的cookie应该变成空。

### cookie npm包
cookie更新必须满足它的格式，比较麻烦。所以我们使用一个npm包来管理：[cookie](https://www.npmjs.com/package/cookie).
安装npm包：
```
npm install cookie
```
更新cookie-sample.js:
```js
const http = require('http');
const cookie = require('cookie');
const uuidV4 = require('uuid/v4');
http.createServer( (req, res) => {
  // parse把cookie变成一个对象
  // req.headers.cookie || '' 的意思: 如果cookie是null，使用空字符串
  let cookies = cookie.parse(req.headers.cookie || '');
  let sessionId = cookies.wechatbaby || '';
  // 如果sessionId是空，生成新的sessionId
  if (sessionId == '') {
    let sessionId = uuidV4();
    // serialize帮助我们合成配置cookie需要的格式
    let sessionIdCookie = cookie.serialize('wechatbaby', sessionId, {
        maxAge: 60 * 60 * 24 // 1 day
    });
    res.setHeader('Set-Cookie', [sessionIdCookie]);
  }
  res.end('Hello Session Id');
}).listen(8080);
```
我们的session id的cookie名字是wechatbaby。先判断这个cookie的值是不是空。如果是空的，生成一个新的uuid，设置cookie有效期为一天。
