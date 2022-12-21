# promise . all(es 2015)vs promise . all settled(es 2020)。什么变了？🤔

> 原文：<https://dev.to/shahzaibkhalid/promise-all-es2015-vs-promise-allsettled-es2020-what-s-changed-3k9d>

嘿！👋

看看今天的`⚡️ Dev Tip 💡`👇

> 关注我的 Twitter [@shahzaibkhalid](https://www.twitter.com/shahzaibkhalid) 以获得更多开发技巧！✨

假设我们想要运行许多承诺来并行执行，并等到它们都准备好。🧐

```
// some dummy promises to play with 🍭
const p1 = new Promise(resolve => resolve('Shahzaib'));
const p2 = new Promise((_ ,reject) => reject('User does not exists!'));
const p3 = new Promise(resolve => resolve('Lahore')); 
```

Enter fullscreen mode Exit fullscreen mode

### Promise.all(如果任何承诺被拒绝，则短路)

如果任何一个通过的承诺被拒绝，由`Promise.all`返回的承诺
会立即拒绝，并显示错误。❌

```
Promise.all([p1, p2, p3])
  .then(response => console.log(response))
  .catch(error => console.log(error)); // 👉'User does not exists!' 
```

Enter fullscreen mode Exit fullscreen mode

### Promise.allSettled(不短路)

等待所有通过的承诺解决(解决或拒绝)。🥳

生成的数组具有:

*   `{status: 'fulfilled', value: result}` -对于成功的响应
*   `{status: 'rejected', reason: error}` -针对错误

```
Promise.allSettled([p1, p2, p3])
  .then(response => console.log(response));

/**
 * 👉 [ {status: 'fulfilled', value: 'Shahzaib'},
 *      {status: 'rejected', reason: 'User does not exists!'},
 *      {status: 'fulfilled, value: 'Lahore'} ] 🚀
 */ 
```

Enter fullscreen mode Exit fullscreen mode

希望你今天学到了新东西。请在下面的评论中让我知道你对这个开发技巧的看法。👀

和平。✌️