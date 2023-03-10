# 异步函数总是返回一个承诺——让我们深入异步世界

> 原文：<https://dev.to/paramharrison/async-functions-always-return-a-promise-lets-deep-into-the-async-world-4adc>

> 我有一个新的免费工作坊，是为使用 Node Js 和 MongoDB 的初学者设计的 *API。这是我的第一个工作坊，让我们一起学习。你可以在这里注册[工作坊](https://www.eventbrite.com/e/api-design-for-beginners-using-node-js-and-mongodb-english-tickets-89601978931)*

异步函数是一种通过等待响应来同步进行异步操作的方法。让我们直接在示例中看到它，

```
// Normal async function - Returning promise
const getCats = async () => {
  return new Promise((resolve, reject) =>
    setTimeout(
      () =>
        resolve([
          {
            name: 'tiger',
          },
          {
            name: 'jimmy',
          },
        ]),
      2000
    )
  );
};

// Self envoking async function
(async () => {
  console.log(getCats()); // Promise { <pending> }
  const cats = await getCats();
  console.log(cats); // [{ name: 'tiger' }, { name: 'jimmy' }]
})(); 
```

如您所见，在我们的示例中，我们正在返回一个承诺。

现在让我们通过返回直接 JSON 数组来看下一个例子，

```
// async function - Returning JSON
const getCatsJson = async () => {
  return [
    {
      name: 'tiger',
    },
    {
      name: 'jimmy',
    },
  ];
};

// Self envoking async function
(async () => {
  console.log(getCatsJson()); // Promise { [{ name: 'tiger' }, { name: 'jimmy' }] }
  const catsJson = await getCatsJson();
  console.log(catsJson); // [{ name: 'tiger' }, { name: 'jimmy' }]
})(); 
```

您可以检查控制台日志，尽管我们从 async 函数返回了一个 JSON 对象。它被包装成一个承诺。因此，我们仍然需要等待和解决的承诺。

使用 promise 方式，您总是可以解决异步问题，而无需等待，

```
getCatsJson().then(console.log); // [{ name: 'tiger' }, { name: 'jimmy' }] 
```

你可以在这里查看例子，

[https://codesandbox.io/embed/async-promise-return-value-fpis9](https://codesandbox.io/embed/async-promise-return-value-fpis9)

希望，这有助于您理解 async 如何通过将值包装成承诺来返回值😋

> 在 [twitter](https://twitter.com/learnwithparam) 上关注我。我在推特上分享我的阅读清单和小贴士