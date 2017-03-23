# 本地服务器
使用node创建一个Web服务是非常方便的，只需要几行代码就可以完成。下面我们将学习用Vim创建一个hello_world.js文件。

### Vim
Vim是非常有名的文本编辑器，但是学习曲线也很陡。因为之后我们需要在Linux服务器上使用简单的Vim命令，所以先在本机上练习一下。建议先阅读一下陈皓翻译的[简明 VIM 练级攻略](http://coolshell.cn/articles/5426.html)的存活部分。

### hello_world.js
运行：
```
vim hello_world.js
```
按i进入Insert模式。复制粘贴以下内容：
``` js
const http = require('http');
http.createServer( (req, res) => {
  res.end('Hello World');
}).listen(8080);
```
按esc退出Insert模式。按:wq保存并退出vim.

执行:
```
node hello_world.js
```
一个简单的Web服务就创建好了。打开浏览器访问: http://localhost:8080/
```
Hello World
```
