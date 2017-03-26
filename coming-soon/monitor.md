# 网站监控
我们需要一些工具来监控网站。一旦网站出了问题，可以立即收到通知。比如，使用[UptimeRobot](https://uptimerobot.com)每5分钟访问你的网站。如果网站掉线了，它会立即发邮件通知你。

但是UptimeRobot无法监控本地运行的网页。你需要按照前面的步骤，把coming-soon.html和coming-soon.js搬到Digital Ocean服务器上，然后用pm2运行coming-soon.js。
