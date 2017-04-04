# Node操作MySQL
如何用Node操作MySQL？谷歌一下，你会发现已经有很多NPM包可以帮我们完成这个任务。我们将使用一个叫[mysql](https://github.com/mysqljs/mysql)的包。

登录DO的服务器，运行以下命令安装mysql包:
```
npm install mysql
```
创建文件mysql-insert.js:
``` js
const mysql      = require('mysql');
// 创建连接对象
// 请自行替换用户名和密码
const connection = mysql.createConnection({
  host     : 'localhost',
  user     : '[username]',
  password : '[password]',
  database : 'wechat'
});
// 连接数据库
connection.connect();
// 创建一个要插入的user对象
// 关于js中对象的用法，请查看https://www.w3schools.com/js/js_objects.asp
const user = {
  username: 'username1',
  password: 'password1',
  email: 'email1',
  verificationCode: 'code1',
  status: 1,
  created: Date.now() / 1000
};
// 执行插入语句
const query = connection.query('INSERT INTO users SET ?', user, function (error, results, fields) {
  if (error) throw error;
  console.log(results);
});
// 执行的语句如下:
// INSERT INTO users SET `username` = 'username1', `password` = 'password1', `email` = 'email1', `verificationCode` = 'code1', `status` = 1, `created` = 1491293579.262
// 这个和insert into ... values ...是一样的。
console.log(query.sql);
// 关闭连接
connection.end();
```
执行
```
node mysql-insert.js
```
添加记录。

创建文件mysql-read.js:
``` js
const mysql      = require('mysql');
//创建连接对象，请自行替换用户名和密码
const connection = mysql.createConnection({
  host     : 'localhost',
  user     : '[username]',
  password : '[password]',
  database : 'wechat'
});
//连接数据库
connection.connect();
//获取所有用户
connection.query('SELECT * FROM users', function (error, results, fields) {
  if (error) throw error;
  console.log(results);
});
//关闭连接
connection.end();
```
执行
```
node mysql-read.js
```
查看记录。

创建文件mysql-update.js:
``` js
const mysql      = require('mysql');
// 创建连接对象
// 请自行替换用户名和密码
const connection = mysql.createConnection({
  host     : 'localhost',
  user     : '[username]',
  password : '[password]',
  database : 'wechat'
});
// 连接数据库
connection.connect();

// 执行更新语句
const query = connection.query('UPDATE users SET email = ? where username = ?', ['email1@gmail.com', 'username1'], function (error, results, fields) {
  if (error) throw error;
  console.log(results);
});
// 执行的语句如下:
// UPDATE users SET email = 'email1@gmail.com' where username = 'username1'
console.log(query.sql);
// 关闭连接
connection.end();
```
执行
```
node mysql-update.js
```
更新记录。

创建文件mysql-delete.js:
``` js
const mysql      = require('mysql');
// 创建连接对象
// 请自行替换用户名和密码
const connection = mysql.createConnection({
  host     : 'localhost',
  user     : '[username]',
  password : '[password]',
  database : 'wechat'
});
// 连接数据库
connection.connect();

// 执行删除语句
const query = connection.query('DELETE FROM users where username = ?', 'username1', function (error, results, fields) {
  if (error) throw error;
  console.log(results);
});
// 执行的语句如下:
// DELETE FROM users where username = 'username1'
console.log(query.sql);
// 关闭连接
connection.end();
```
执行
```
node mysql-delete.js
```
删除记录。
