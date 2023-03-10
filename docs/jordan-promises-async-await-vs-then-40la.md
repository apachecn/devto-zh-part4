# Jordan promises–异步/等待 vs .then

> 原文：<https://dev.to/aarmora/jordan-promises-async-await-vs-then-40la>

正如我在许多其他帖子中所说的，我是 async/await 的忠实粉丝。我认为这是一种非常干净的管理代码的方式，包括同步和异步代码。

## 异步/等待牛逼

我想比较一下 async/await 可以避免的一些不好的地方。

[![](img/c2a135202fc90730092e05763f6a1ddb.png)](https://i.giphy.com/media/XuLXZa0PJNiV2/giphy.gif)T3】

```
// Yucky then usage
async function getThePizza() {
    functionGettingFromMongo('someArguments').then((someData) => {

        // We want to use this data so we need to know that this promise completed
        doSomeWorkWithAPromise().then((moreData) => {
            const somethingCool = someData + moreData;

            insertToMongoNow(somethingCool).then((response) => {
                console.log('success!', response);
            }).catch((e) => {
                console.log('some error happened', e);
            });
        });

    });
} 
```

这是一个函数，我们正在进入回调地狱。虽然`.then()`不是一个回调函数，但从代码上看，它读起来完全一样。它是一堆嵌套的函数，越来越深，使得代码越来越不可读。

[![](img/ea737df048f35ac53a6a9ef1e4116de8.png)](https://i.giphy.com/media/12zfAjyQ3RZNSw/giphy.gif)T3】

```
// So good.
async function getThePizza() {
    const someData = await functionGettingFromMongo('someArguments');
    const moreData = await doSomeWorkWithAPromise();

    const somethingCool = someData + moreData;
    try {
        const response = await insertToMongoNow(somethingCool );
        console.log('success!', response);
    }
    catch(e) {
        console.log('some error happened', e);
    }

} 
```

可读性强多了。至少不会越来越缩进。自从我发现 async/await 以来，我一直在独占使用它，以至于我开始认为`.then()`不好，并且有点忘记了编程的美妙之处在于几乎所有事情都是视情况而定的。

## 当`.then()`没那么糟糕

当我试图一次抓取很多页面时，我不想被阻塞。我没有使用 await，而是将承诺放入一个数组中，并使用`Promise.all`。这将确保我知道什么时候一切完成，这样我就可以关闭我的数据库连接或木偶浏览器。像这样:

```
async function getThePizza() {

    const promises: any[] = [];
    for (let url of lotsAndLotsOfUrls) {
        promises.push(functionForHandlingTheUrl(url));
    }

    await Promise.all(promises);
    await closeMyDbConnectionOrPuppeteerOrSomething();
} 
```

这很好。*除了*当我在`functionForHandlingTheUrl`完成后想对特定的 url 做些什么的时候。现在怎么办？使用`await`看起来像这样:

```
async function getThePizza() {

    for (let url of lotsAndLotsOfUrls) {
        const something = await functionForHandlingTheUrl(url);
        await dbWorkOrSomething(something);
    }

    await closeMyDbConnectionOrPuppeteerOrSomething();
} 
```

可读性很强，但速度很慢。我不再利用 I/O 线程的任何并发性。那么更好的选择是什么呢？这很有效:

```
async function getThePizza() {

    const promises: any[] = [];
    for (let url of lotsAndLotsOfUrls) {
        promises.push(functionForHandlingTheUrl(url).then(something => {
            // This parts only happens when functionForHandlingTheUrl completes and doesn't block the rest
            return dbWorkOrSomething(something);
        }));

    }

    await Promise.all(promises);
    await closeMyDbConnectionOrPuppeteerOrSomething();
} 
```

在这里使用`.then()`使得我有一个嵌套函数，这是真的，但是我们能够在只有特定的迭代准备好的时候做事情。它不会阻碍其余的循环，所以我仍然快如闪电。我很开心。

[![](img/f46ea7ae46f1b02584589e92968b7d53.png)](https://i.giphy.com/media/9Jcw5pUQlgQLe5NonJ/giphy.gif)

小 PS。我的许多特色图片来自 Unsplash.com。这是一个非常好的资源。今天的来自[康纳·乔利](https://www.connorthejolley.com/)。谢谢康纳！

帖子[Jordan promises–async/await vs . then](https://javascriptwebscrapingguy.com/jordan-promises-async-await-vs-then/)最早出现在 [JavaScript Web Scraping Guy](https://javascriptwebscrapingguy.com) 上。