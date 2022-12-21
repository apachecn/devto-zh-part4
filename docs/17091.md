# jordan 利用 node.js 中的多线程 io

> 原文：<https://dev.to/aarmora/jordan-takes-advantage-of-multithreaded-i-o-in-nodejs-35>

[此处示例代码](https://github.com/aarmora/jordan-does-dead-link-checking)

## 死链接检查永不失效

我已经用这个库玩了三到四个星期了。这有点像是我在检查一个永远不会死的存储库的死链接。我其实不是在抱怨。使用相同的代码库并尝试几种不同的方法来实现相同的目标是很好的。我可以评估每种方法的性能，并决定哪种是最好的。

第一个帖子没有利用 javascript 的异步特性进行死链接检查，所以它会检查一个链接，等待检查完成，然后继续下一个。这慢得令人难以置信。刚写的时候没意识到有多慢。作为基线，检查这个网站(javascriptwebscrapingguy.com)上的死链接需要 **~120 秒**。

在第二篇文章中，我想加快速度，并开始使用一些工作线程。现在回想起来，我可以看到它是多么的简陋。我自己管理线程数，效率不是很高。我在这个网站(javascriptwebscrapingguy.com)上用了 20 个线程，用了 **~28 秒**。

事情开始变得好了很多。我着重于为工作线程使用一个池队列。该池会自动对任务进行排队，当一个任务完成时，它会引入另一个任务。代码更加简洁，在这个网站上使用(javascriptwebscrapingguyg.com)需要 **~20 秒**。

## 最终形态？

我想我已经达到了我的最终形态。除了请求之外，它不需要任何额外的包。它很快。代码非常简单。代码中有几个主要的不同之处。

```
const options: requestPromise.RequestPromiseOptions = {
            method: 'GET',
            resolveWithFullResponse: true,
            timeout: 10000,
            agentOptions: {
                maxSockets: 4
            }
        }; 
```

第一个是我实际发出请求调用的地方，我只是添加了一个带有`maxSocets`选项的`agentOptions`。在下面的情况下，这将把打开的 I/O 线程的数量限制为 4。

```
let links: ILinkObject[] = await getLinks(html, domain, domain);
    const promises: any[] = [];

    for (let i = 0; i < links.length; i++) {
        if (!links[i].status) {
            promises.push(checkLink(links[i], links, domain));
        }
    }

    await Promise.all(promises); 
```

在最初的功能中，`findDeadLinks`，我将有多少链接，我从刮域的主页。当我循环的时候，我像以前一样调用`checkLink`，但是我没有用`await`阻塞。相反，我将它放入一个承诺数组中，并用`await Promise.all(promises);`阻塞下面。

我做的下一个改变是在`checkLink`函数中。在这种情况下，我只是将函数改为递归的，同样，我没有用`await`阻塞，而是将返回结果推入一个承诺数组，然后等待它们全部用`await Promise.all(promises);`解决。

```
// Replace the link we were checking with the completed object
    let linkToReplaceIndex = links.findIndex(linkObject => linkObject.link === linkObject.link);
    links[linkToReplaceIndex] = linkObject;
    const promises: any[] = [];

    for (let linkToCheck of newLinks) {
        if (links.filter(linkObject => linkObject.link === linkToCheck.link).length < 1) {
            // console.log('pushed in ', linkToCheck.link);
            links.push(linkToCheck);

            promises.push(checkLink(linkToCheck, links, domain));
        }
    }

    await Promise.all(promises);

    return Promise.resolve({ link: linkObject, links: links }); 
```

## 穿越真实生活场景

我想试着介绍一下这在实践中是如何工作的。比方说，我登陆 javascriptwebscrapingguy.com，在主页上找到 37 个链接。然后我将遍历它们并调用`checkLinks` 37 次。我等待他们都解决他们的承诺，因为`checkLinks`是递归调用自己，这不会发生，直到我们完成。

每次它调用`checkLinks`时，都会得到一组新的链接。如果它检查的第一个链接是

[https://JavaScript webscrapingguy . com/Jordan-is-speed-accelerating-scraping-with-multi-threads/](https://javascriptwebscrapingguy.com/jordan-is-speed-speeding-up-scraping-with-multiple-threads/)它将从该页面获取所有链接，然后在这些链接上调用`checkLinks`。当所有这些都从

[https://JavaScript web scraping guy . com/Jordan-is-speed-accelerating-scraping-with-multiple-threads/](https://javascriptwebscrapingguy.com/jordan-is-speed-speeding-up-scraping-with-multiple-threads/)解析时，那么 promise 将被解析，1/37 将从初始循环中被解析。

## 结果

|**|**|**速度**|
| 1 | 38.373 |
| 1 | 31.621 |
| 1 | 31.4 |
| 2 | 22.687 |
| 2 | 22.644 |
| 2 | 23.101 |
| 3 | 19.552 |
| 3 | 19.578 |【T11
| 4 | 18.103 |
| 4 | 17.353 |
| 5 | 17.686 |
| 5 | 18.743 |
| 5 | 17.599 |
| 5 | 19.006 |
| 5 | 19.607 |
| 8 | 17.97 |
| 8 | 18.278 |【T28

你可以看到使用 4 个以上的`maxSockets`似乎根本不能提高性能。这让我很惊讶，在某种程度上我想调查一下原因。为什么在使用有 20 个工作线程的线程池而不是多个 I/O 连接时，它的工作速度更快？

就在那里。快！这样做的话，我总是能在 **17 到 18 秒**之间完成工作。这比使用网络工作者要快得多。相当酷。

[此处示例代码](https://github.com/aarmora/jordan-does-dead-link-checking)

帖子 [Jordan 利用 Nodejs 中的多线程 I/O](https://javascriptwebscrapingguy.com/jordan-takes-advantage-of-multithreaded-i-o-in-nodejs/)最早出现在 [JavaScript Web Scraping Guy](https://javascriptwebscrapingguy.com) 上。