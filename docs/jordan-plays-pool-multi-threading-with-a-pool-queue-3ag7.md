# Jordan 玩撞球(使用一个撞球队列的多线程)

> 原文：<https://dev.to/aarmora/jordan-plays-pool-multi-threading-with-a-pool-queue-3ag7>

[此处示例代码](https://github.com/aarmora/jordan-does-dead-link-checking/tree/with-pools)

## Reddit 评论人

我真的很喜欢 reddit。我喜欢在几乎任何话题上都能找到真实人物的反馈。很多时候反馈来得非常快。我在 r/node 上分享了[我的最后一篇帖子](https://javascriptwebscrapingguy.com/jordan-is-speed-speeding-up-scraping-with-multiple-threads/)，得到了一个有趣而准确的[评论](https://www.reddit.com/r/node/comments/byx2cu/using_multiple_threads_and_web_workers_to/eqor8bf?utm_source=share&utm_medium=web2x)。

u/m03geek 准确地指出我的脚本没有充分利用多线程的能力。我的[多线程分支](https://github.com/aarmora/jordan-does-dead-link-checking/tree/with-threads)在链接检查时加快了很多速度，但是我是如何做的(见下文)只是一次运行 10 个线程(或者我想运行多少线程)，然后它会等待。如果其中的 8 个任务完成得非常快，那么它们就会闲置下来，等待另外两个任务完成。

```
const promises: any[] = [];

            const amountOfThreads = 10;
            for (let linkToCheckIndex = 0; linkToCheckIndex < amountOfThreads; linkToCheckIndex++) {
                if (links[i + linkToCheckIndex]) {
                    promises.push(checkLink(links[i + linkToCheckIndex], domain));
                }
            }

            const checkLinkResponses = await Promise.all(promises); 
```

老实说，我一直在研究如何让池和池队列工作。我甚至没有想到这个具体的事情，但他是正确的。我今天的帖子是相同的链接检查器库，除了使用池。结果相当不错。

我认为同样值得注意的是，u/m03geek 还提到“但是 links checker 并不是使用 workers 的好例子，因为 node 有多线程 i/o，而您(或该主题的作者)所需要的只是一个简单的队列，将正在进行的请求的最大数量限制在 20 个或 50 个。”我希望下周尝试比较利用多线程 i/o 到池队列的速度。

## 结果

让我们先看一下结果。为了更容易阅读，我将把它们放在一个列表中，然后我会贴上截屏。我们正在检查 198 个链接，他们都成功地找到了相同的坏链接。

*   普通单线程， **128.492 秒**
*   10 线程老办法，推至一个承诺， **38.147 秒**
*   限制 20 个线程的池， **22.720 秒**
*   限制 10 个线程的池， **20.927 秒**
*   限制为 8 个线程的池， **22.913 秒**
*   有 6 个线程限制的池， **26.728 秒**
*   限制为 4 个线程的池， **35.252 秒**
*   限制为 2 个线程的池， **62.526 秒**

我认为有趣的是，20 个线程的性能实际上比 10 个线程差。差不了多少，但我测试了 3-4 次，20 次总是花费更长的时间。我意识到 3-4 倍并不是一个可靠的样本大小，但至少从 10 到 20 的改进是很难的。

我认为讨论具有 2、4、6 和 8 个线程的池之间的显著差异也很有趣。我实际上只有四个内核，所以 4 个内核之后的差别应该不会太明显。虽然在 4 之后改进速度有所减慢，但这仍然是值得的。2 和 4 之间显然有着巨大的差异。

## 代码修改

该代码建立在我们之前的代码之上，因此我将只关注我为在池中使用线程而更改的部分。你可以在 github 上的[分支找到完整的代码。在](https://github.com/aarmora/jordan-does-dead-link-checking/tree/with-pools) [threads.js 库](https://github.com/andywer/threads.js/)也有进一步的文档，由[安迪](https://twitter.com/andywritescode)编写，他非常有帮助并且反应迅速。

```
const spawnLinkChecker = () => {
        return spawn(new Worker('./../../../../dist/checkLinkWorker.js'));
    }
    const pool = Pool(spawnLinkChecker, 10);
    for (let i = 0; i < links.length; i++) {
        if (!links[i].status) {
            pool.queue(linkChecker => linkChecker(links[i], domain));
        }
    }

    pool.events().subscribe((event) => {
        if (event.type === 'taskCompleted' && event.returnValue.links) {
            console.log('task completed', new Date());

            // Replace the link we were checking with the completed object
            let linkToReplaceIndex = links.findIndex(linkObject => linkObject.link === event.returnValue.link.link);
            links[linkToReplaceIndex] = event.returnValue.link;

            for (let linkToCheck of event.returnValue.links) {
                // We want to check if we've already checked this link
                if (links.filter(linkObject => linkObject.link === linkToCheck.link).length < 1) {
                    console.log('pushed in ', linkToCheck.link);
                    links.push(linkToCheck);

                    pool.queue(linkChecker => linkChecker(linkToCheck, domain));
                }
            }
        }
    }); 
```

使用池实际上清理了很多东西。我只是像以前一样从域名主页获取所有链接，然后通过一个循环将它们全部放入池队列。在上面的例子中，我将我的工作人员限制设置为 10 人，当作业完成时，池将自动保持工作继续进行。

我真的很担心能够更新我正在检查的链接，然后用这种方式处理发现的新链接，但是订阅`pool.events()`却是小菜一碟。我只是观察`taskCompleted`事件，然后处理`returnValue`，它包括更新状态的链接和新链接。我遍历这些新链接，添加我以前没有的链接，然后立即将它们放入池队列，让它继续它的魔力。

感觉真的像是魔术。安迪在这个图书馆里做了一件了不起的工作。我真的很感谢像他这样的了不起的人，是他们让软件社区变得如此神奇。人们只是为了能够创造事物的酷感而创造事物。

[此处示例代码](https://github.com/aarmora/jordan-does-dead-link-checking/tree/with-pools)

[![](img/de715f6043eb25354f70b65083c765f1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6IW8VbPR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://javascriptwebscrapingguy.com/wp-content/uploads/2019/06/normal-single-threaded-request.png) 

<figcaption>单线程结果</figcaption>

[![](img/b5375e064d876aed1acf558e21bc45db.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1p4pIcxQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://javascriptwebscrapingguy.com/wp-content/uploads/2019/06/with-10-threads.png) 

<figcaption>没有使用 10 个线程的池</figcaption>

[![](img/1d7334a65c9bc868197083e5ec4bd311.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hZBBTav2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://javascriptwebscrapingguy.com/wp-content/uploads/2019/06/pool-with-20-limit.png) 

<figcaption>用 20 个线程限制池</figcaption>

[![](img/fd1e5edbd9b7e012c1330236b3e6a886.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kd7H2rA7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://javascriptwebscrapingguy.com/wp-content/uploads/2019/06/pool-with-10-limit.png) 

<figcaption>用 10 个线程限制池</figcaption>

[![](img/e6fa3981698cc02f62089d0f897e4412.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--coHtx2F7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://javascriptwebscrapingguy.com/wp-content/uploads/2019/06/pool-with-8-limit.png)

<figcaption>8 线程池限制</figcaption>

[![](img/79556e847bfc9fca40521949545ed04b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LEPoH8Cp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://javascriptwebscrapingguy.com/wp-content/uploads/2019/06/pool-with-6-limit.png)

<figcaption>6 线程限制池</figcaption>

[![](img/38d694c46bc05f929bc1b855b44c217f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RWXvaD-G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://javascriptwebscrapingguy.com/wp-content/uploads/2019/06/pool-with-4-limit.png)

<figcaption>4 线程限制池</figcaption>

[![](img/2bda8d37e2215780fedc79e170599951.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8WXU8cHy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://javascriptwebscrapingguy.com/wp-content/uploads/2019/06/pool-with-2-limit.png) 

<figcaption>用 2 个线程限制池</figcaption>

帖子[Jordan play Pool](https://javascriptwebscrapingguy.com/jordan-plays-pool-multi-threading-with-a-pool-queue/)最早出现在 [JavaScript Web Scraping Guy](https://javascriptwebscrapingguy.com) 上。