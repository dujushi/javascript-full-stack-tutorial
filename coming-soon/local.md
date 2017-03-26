# 本地预览
浏览器可以直接执行本地的HTML文件。所以前端的工作完全不需要服务器，也可以完成。

创建一个coming-soon.html文件。粘贴以下HTML:
``` html
<html>
  <body>
    <h1>Coming Soon ...</h1>
  </body>
</html>
```
双击文件，就可以在浏览器上看到标题样式的Coming Soon。

添加一张图片, 然后刷新页面查看效果:
``` html
<html>
  <body>
    <h1>Coming Soon ...</h1>
    <img src="https://dw9to29mmj727.cloudfront.net/misc/newsletter-naruto3.png" />
  </body>
</html>
```

用CSS修改一下样式:
``` html
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
    <h1>Coming Soon ...</h1>
    <img src="https://dw9to29mmj727.cloudfront.net/misc/newsletter-naruto3.png" />
  </body>
</html>
```

用Javascript显示当前时间:
``` html
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
    <h1>Coming Soon ...</h1>
    <img src="https://dw9to29mmj727.cloudfront.net/misc/newsletter-naruto3.png" />
    <div id="date_time"></div>

    <script>
    var dateTimeDiv = document.getElementById("date_time");
    dateTimeDiv.innerHTML = new Date();
    </script>    
  </body>
</html>
```
