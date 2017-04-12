# 登录
每次浏览一个网页，都是一次Http Request。浏览器发送Http Request给Web服务器。Web服务器根据Http Request做相应的处理，返回一个Http Response给浏览器。浏览器根据Http Response做相应的处理，要么跳转到新的页面，要么渲染页面给用户查看。

Http有一个非常重要的属性叫无状态(stateless)。每个Http Request之间都是独立的。后面的request不知道前面的request的任何信息。也就是说，我在login页面登录了，然后去访问其他页面的时候，其他页面并知道我登录过。所以人们引入了两个概念: cookie和session.

cookie: 保存在浏览器端。每次发送Http Request的时候，浏览器会把所有的cookie发送给服务器。

session: 保存在服务器端。用户第一次访问网页的时候，我们给它生成一个session id，并且把session id保存到cookie里。我们可以把用户的登录信息，购物车信息保存到session里，用session id区分开来。这样每次浏览器传来session id之后，我们就可以在session里找到对应的用户信息。

cookie的基础知识请查看：[英文](https://www.w3schools.com/js/js_cookies.asp)，[中文](http://www.w3school.com.cn/js/js_cookies.asp).
