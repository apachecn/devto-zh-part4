# Contrib:将 Nodemailer 与 Express 和 Templating 一起使用

> 原文：<https://dev.to/th3n00bc0d3r/contrib-using-nodemailer-with-express-and-templating-3o68>

你好观众，

我最近偶然发现了 dev.to 的一个用户，他想帮助 nodemailer 使用 express。作为一个初学者，有时所有的文档都有点混乱，你只需要让事情进行下去，所以我想我会与你们分享这一点。我做了一个小 github，希望对你有帮助。

## 必填

*   节点和 NPM
*   使用 SMTP 的电子邮件帐户

Github:https://github.com/th3n00bc0d3r/Nodemailer-Express-Node

从命令行

``
git 克隆[https://github.com/th3n00bc0d3r/Nodemailer-Express-Node](https://github.com/th3n00bc0d3r/Nodemailer-Express-Node)
CD Node mailer-Express-Node
NPM 安装
在编辑器中打开 app.js，更改 smtp 设置`

 `node app.js
`

 `你需要启动一个 GET 调用到[http://localhost:3000/send/mail](http://localhost:3000/send/mail)或者在浏览器中打开它。``