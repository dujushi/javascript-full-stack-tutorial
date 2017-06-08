# LiveReload
[LiveReload](https://www.npmjs.com/package/livereload)实时监控代码修改，更新页面，省去刷新浏览器的步骤。

本地安装livereload npm包。注意：不是在Dockerfile里。
``` sh
npm install -g livereload
```

代码目录运行:
``` sh
# -w 1000
# 延时一秒钟执行。给nodemon重启应用的时间。否则常常出现页面不能访问的错误。
livereload . -w 1000
```

Chrome浏览器安装LiveReload扩展。地址栏后面就会出现一个Enable LiveReload的图标。在Hello World页面，点击此图标开启LiveRelod。更新server.js的Hello World为Hello LiveReload。观察页面变化。
