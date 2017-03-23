# Digital Ocean
本文我们一起申请一个Digital Ocean服务器，然后在服务器上运行Hello World程序。别人就可以通过服务器的IP直接访问你的网站了。

### 注册DO
通过这个[链接](https://m.do.co/c/a08330f9bf19)注册DO的账号，你可以得到10美金的使用额度。DO最便宜的服务器每个月只需要5美金。也就是说，你可以免费使用两个月:P

### 创建服务器
注册成功之后，点击右上角的Create Droplet，开始创建自己的服务器:
1. Choose an image: 默认Ubuntu
2. Choose a size: 选择最便宜的:P
3. Choose a datacenter region: 选择离你近的数据中心

点击Create创建。创建成功之后，DO会给你发一封邮件, 包含与以下内容相似的服务器登录信息：
```
Droplet Name: ubuntu-512mb-nyc3-01
IP Address: 45.55.165.85
Username: root
Password: 8c83a2b2ff70dbd89d6ed9dc48
```
打开Terminal, 运行：
```
ssh root@45.55.165.85
```
复制粘贴密码即可登录服务器。首次登录会要求修改密码。

### hello_world.js
按照前面章节，安装nvm和node。

运行：
```
vim hello_world.js
```
按i进入Insert模式。复制粘贴以下内容：
``` js
const http = require('http');
http.createServer( (req, res) => {
  res.end('Hello World');
}).listen(80);
```
按esc退出Insert模式。按:wq保存并退出vim.

注意！这里的listen函数使用80端口。因为http使用的是80端口。

执行:
```
node hello_world.js
```
打开浏览器访问: http://45.55.165.85
```
Hello World
```
