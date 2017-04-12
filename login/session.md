# Session
Session可以保存在内存，文件，或者数据库里。这里我们采用保存到内存的方法。定义一个变量sessions用来储存用户信息。

创建session-sample.js:
```js
const http = require('http');
const cookie = require('cookie');
const uuidV4 = require('uuid/v4');
// 定义一个sessions数组用于保存所有的session
// 这里的sessions是一个关联数组。详见：https://www.w3schools.com/js/js_arrays.asp
let sessions = [];
http.createServer((req, res) => {
  let cookies = cookie.parse(req.headers.cookie || '');
  let sessionId = cookies.wechatbaby || '';
  if(sessionId == ''){
    let sessionId = uuidV4();
    let sessionIdCookie = cookie.serialize('wechatbaby', sessionId, {
        maxAge: 60 * 60 * 24 // 1 day
    });
    res.setHeader('Set-Cookie', [sessionIdCookie]);
  }
  // 当前的时间戳
  // 时间戳的概念: http://baike.baidu.com/item/%E6%97%B6%E9%97%B4%E6%88%B3
  const currentTimestamp = Date.now() / 1000;
  // 定义一个session变量
  let session = null;
  // 判断sessions是否有sessionId的key
  if(sessionId in sessions){
    session = sessions[sessionId];
    // 更新最后一次访问网站的时间
    session.lastTimestamp = currentTimestamp;
  }else{
    // 创建一个全新的session
    session = {
      createdTimestamp : currentTimestamp,
      lastTimestamp : currentTimestamp
    }
    // 用sessionId作为key保存session
    sessions[sessionId] = session;
  }
  const secondsSpent = session.lastTimestamp - session.createdTimestamp;
  res.end(`你已经在本网站停留${secondsSpent}秒！`);
}).listen(8080);
```
这里我们用session保存用户第一次和最后一次访问网站的时间戳，从而计算出用户在网站停留的时间。当我们实现登录功能的时候，用session保存用户登录信息，比如用户名，id等。
