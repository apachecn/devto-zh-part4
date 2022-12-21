# node.js 如何发送邮件

> 原文：<https://dev.to/miku86/nodejs-how-to-send-an-email-2nha>

## [T1】简介](#intro)

所以我们[在我们的机器上安装了 NodeJS](https://dev.to/miku86/what-is-nodejs-how-to-install-it-1900) 。

我们也知道[如何获得外部包](https://dev.to/miku86/nodejs-how-to-get-external-packages-npm-56oj)。

现在我们想学习如何使用 [nodemailer](https://www.npmjs.com/package/nodemailer) 发送电子邮件。

## 写一个简单的脚本

*   打开你的终端
*   创建一个名为`index.js`的文件:

```
touch index.js 
```

Enter fullscreen mode Exit fullscreen mode

*   将这段 JavaScript 代码添加到其中:

```
// import nodemailer (after npm install nodemailer)
const nodemailer = require('nodemailer');

// config for mailserver and mail, input your data
const config = {
  mailserver: {
    host: 'smtp.ethereal.email',
    port: 587,
    secure: false,
    auth: {
      user: 'yutfggtgifd7ixet@ethereal.email',
      pass: 'tX29P4QNadD7kAG7x5'
    }
  },
  mail: {
    from: 'foo@example.com',
    to: 'bar@example.com',
    subject: 'Hey',
    text: 'Testing Nodemailer'
  }
};

const sendMail = async ({ mailserver, mail }) => {
  // create a nodemailer transporter using smtp
  let transporter = nodemailer.createTransport(mailserver);

  // send mail using transporter
  let info = await transporter.sendMail(mail);

  console.log(`Preview: ${nodemailer.getTestMessageUrl(info)}`);
};

sendMail(config).catch(console.error); 
```

Enter fullscreen mode Exit fullscreen mode

注意:Nodemailer 有很多可用的设置，因此请阅读 nodemailer 的文档[。](https://nodemailer.com)

* * *

## 从终端运行它

*   运行它:

```
node index.js 
```

Enter fullscreen mode Exit fullscreen mode

*   结果:

```
Preview: https://ethereal.email/message/XWk2jZDkEStePsCvXWk60Yf74VUAhgNZAAAACQqQo2lpzFsxaciWAqd9ZjY 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 进一步阅读

*   [NPM 上的 nodemailer](https://www.npmjs.com/package/nodemailer)
*   [nodemailer docs](https://nodemailer.com)
*   [配置邮件服务器](https://nodemailer.com/smtp)
*   [配置邮件](https://nodemailer.com/message)

* * *

## 提问

*   你最喜欢的在 Node 发送邮件的方式/包是什么？
*   你用节点邮件自动化一些任务吗？