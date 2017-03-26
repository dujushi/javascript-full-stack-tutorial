# PM2
我们一直是用node命令提供web服务, 比如:
```
node coming-soon.js
```
此时，如果你关闭终端，web服务也就停止了。我们可以使用一些工具来保证web服务24小时运行，比如[PM2](https://github.com/Unitech/pm2).

安装PM2:
```
npm install pm2 -g
```
运行coming-soon.js:
```
pm2 start coming-soon.js
```
查看pm2下的所有进程:
```
pm2 list
```
杀死某个进程:
```
pm2 stop 0                    # Stop process with id 0
```

想了解更多解决方案，请参考: [Linux 守护进程的启动方法](http://www.ruanyifeng.com/blog/2016/02/linux-daemon.html)
