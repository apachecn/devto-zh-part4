# 循环中的承诺- Javascript ES6

> 原文：<https://dev.to/ganeshmani/promises-inside-a-loop-javascript-es6-19p4>

在本文中，我们将看到如何在循环中编写一个承诺函数。循环中的承诺- Javascript ES6

如果你想学习更多关于 Javascript 的知识。看看这篇文章

**[原型继承- Javascript 周刊](https://cloudnweb.dev/2019/07/prototypal-inheritance-javascript-weekly/)**

**[理解 Javascript 中的闭包- Javascript 周刊](https://cloudnweb.dev/2019/07/understanding-closures-in-javascript-javascript-weekly/)**

**[为节点/快车配置通天塔](https://cloudnweb.dev/2019/07/configuring-babel-for-node-js-express-server/)**

[订阅我的简讯](http://eepurl.com/gvbr7j)获取更多更新。

在开始编写代码之前，我们将了解为什么需要在循环中编写一个 promise 函数。

### 方案

考虑这样一种情况，您需要向电子邮件列表发送邮件。你将编写一个可以一次发送一封邮件的函数。

假设你有一个类似这样的电子邮件列表

```
const emailList = ['ganesh@gmail.com','john@gmail.com','tarry@gmail.com']
```

首先，编写一个函数，它接受一个 emailId，并向相应的邮箱发送一封邮件。

```
function sendMail() {
  //Sending mail
}
```

您需要根据数组中的电子邮件数量来调用该函数。

你可以想到，为什么我不能调用 for 循环里面的函数。这很简单..对吗？？。

不...那是行不通的。因为发送邮件是一个异步函数。循环不会等到前一个函数完成

因此，可能会错过函数调用或发送邮件。要解决这个问题，我们需要用**承诺**。

### 循环中的承诺- Javascript ES6

首先，在返回 **[承诺](https://medium.com/javascript-scene/master-the-javascript-interview-what-is-a-promise-27fc71e77261)** 的函数中编写邮件发送逻辑。

```
 const sendEmail = (userEmail) => {

  return new Promise(async (resolve,reject) => {
        //this is a mock email send logic
        setTimeout(() => {

            resolve(`Email Sent to ${userEmail}`);
        },3000)

  })
}
```

调用 Javascript 里面的函数 **map()** 函数和 **[Promise.all](https://www.taniarascia.com/promise-all-with-async-await/)**

```
const  sendEmails = async () => {

    const userEmails = ['ganesh@gmail.com','john@gmail.com','Sam@gmail.com'];

    const status = await Promise.all(userEmails.map(email => sendEmail(email)));

    console.log("Status =>",status);

}
```

这是在循环中调用 promise 的方法之一。

[https://repl.it/@ganeshmani/Promiseloop?lite=true](https://repl.it/@ganeshmani/Promiseloop?lite=true)

总而言之，承诺是 Javascript ES6 中一个强大的附加功能。循环中的承诺- Javascript ES6

此外，我们将在接下来的文章中看到更多关于 Javascript 核心概念的内容。敬请关注。

直到那时快乐编码:-)