# 生成验证码
我们给每个注册账号生成一个验证码，放到邮箱的链接里。用户需要点击链接激活账号。验证码需要是唯一的。我们可以基于JS的[random函数](https://www.w3schools.com/jsref/jsref_random.asp)写一个验证码生成器。这里我们直接使用npm包: [uuid](https://www.npmjs.com/package/uuid). [UUID](https://en.wikipedia.org/wiki/Universally_unique_identifier)是全局唯一标示符。

安装uuid:
```
npm install uuid
```
创建uuid-sample.js:
``` js
const uuidV4 = require('uuid/v4');
const uuid = uuidV4();
console.log(uuid);
//uuid: '110ec58a-a0f2-4ac4-8393-c866d813b8d1'
```
每次生成的值都是唯一的。
