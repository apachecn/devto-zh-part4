# 乔丹比较刮与阻塞和非阻塞呼叫

> 原文：<https://dev.to/aarmora/jordan-compares-scraping-with-blocking-and-non-blocking-calls-bhe>

[演示代码在这里](https://github.com/aarmora/jordan-compares-scraping-with-blocking-and-non-blocking-calls)

## 招生

[![](img/82807ded3adc354789f3e6bb90bcc8b0.png)](https://i.giphy.com/media/vfQ0bV5kqcwRG/giphy.gif)

好吧，我承认。我太依赖 async / await 了。这让我的大脑更容易组织代码的运行方式。这没什么大不了的，除非我没有利用 Node 出色的异步 I/O，它确实在 web 抓取中发挥了很大作用。

## 代码

[![](img/ccbaa4981b5cedadaa86504f9c4b8351.png)](https://i.giphy.com/media/QpMBrwCZfb9yE/giphy.gif)

示例代码非常简单。我使用 [Axios](https://github.com/axios/axios) 作为我的请求库。这是一个基于 promise 的库，使用起来非常简单，正如我在[这篇文章](https://javascriptwebscrapingguy.com/jordan-makes-his-http-requests-more-consistent/)中所讨论的，这个库让我更加成功，没有被网站屏蔽。

```
const websites = [
    'https://amazon.com',
    'https://google.com',    
 'https://www.reddit.com/r/Entrepreneur/comments/cwjkcm/launching_a_service_that_provides_business_leads/',
    'https://www.reddit.com/r/Entrepreneur/',
    'https://bing.com',
    'https://reddit.com',
    'https://linkedin.com',
    'https://yahoo.com',
    'https://www.reddit.com/r/funny/',
    'https://youtube.com',
    'https://twitter.com',
    'https://instagram.com',
    'https://www.amazon.com/dp/B07HB2KL4C',
    'https://www.amazon.com/dp/B01LTHP2ZK', 'https://www.reddit.com/r/funny/comments/cwnmwv/not_a_huge_fan_of_the_new_candy_machine_at_work/',
    'https://boise.craigslist.org/',
    'https://boise.craigslist.org/d/office-commercial/search/off',
    'https://boise.craigslist.org/d/science-biotech/search/sci',
    'https://baidu.com',
    'https://msn.com',
    'https://www.ebay.com/deals/home-garden'

];

// scrapeWithAwait(websites);
scrapeWithoutAwait(websites); 
```

我有一个很大的随机网站列表，然后我将它们作为参数传递给`scrapeWithoutAwait`或`scrapeWithAwait. scrapeWithAwait`调用这些网站，但等待每个网站完成。我承认我以前经常这样做，甚至没有意识到它让我慢了这么多。却没有意识到有更好的方法。

代码是这样的:

```
async function scrapeWithAwait(websites: string[]) {

    const overallStartTime = new Date();
    for (let i = 0; i < websites.length; i++) {

        const startTime = new Date();

        let axiosResponse;
        try {
            axiosResponse = await axios({
                method: 'GET',
                url: websites[i],
                headers: { 'User-Agent': "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/74.0.3729.169 Safari/537.36 " },
                timeout: 7000
            });
            console.log('successful axios call!', axiosResponse.status, Date.now() - +(startTime), websites[i]);
        }
        catch (error) {
            if (error.response) {
                console.log(' Error getting website from response', websites[i], error.response.status);
                status = error.response.status;
            }
            else if (error.request) {
                console.log(' Error getting website from request', error.request.message, websites[i]);
            }
            else {
                console.log(' Eome other error', error.message);
            }

        };
    }

    console.log('Finished with blocking calls', Date.now() - +(overallStartTime));
} 
```

代码开始并简单地向网站发出一个 axios 调用，等待直到它完成，然后调用下一个。这个方法确保我们得到一个返回的结果，我们一直等到我们得到那个结果，直到我们执行下一部分代码。检查三次尝试这样做的总时间。

[![](img/c43d6f4e98418374ebe5d6cad0b6ff9f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LXYfbObU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://javascriptwebscrapingguy.com/wp-content/uploads/2019/08/with-blocking-1.png) 

<figcaption>带阻塞，20446 ms 完成</figcaption>

[![](img/8940277fa3f7c463260aa5215642874d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--r_BIsSMQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://javascriptwebscrapingguy.com/wp-content/uploads/2019/08/with-blocking-2-2.png) 

<figcaption>同闭锁，完成于 21550 ms</figcaption>

[![](img/bce001acabfa2c299e5f96c6f8560cdb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5nGEmegX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://javascriptwebscrapingguy.com/wp-content/uploads/2019/08/with-blocking-3-1.png) 

<figcaption>带闭锁，20996 年完成 ms</figcaption>

这三次尝试都在大约 20 秒内完成。

现在我将在不阻塞调用的情况下查看代码。

```
async function scrapeWithoutAwait(websites: string[]) {

    const overallStartTime = new Date();
    const promises: any[] = [];
    for (let i = 0; i < websites.length; i++) {

        const startTime = new Date();
        promises.push(axios({
            method: 'GET',
            url: websites[i],
            headers: { 'User-Agent': "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/74.0.3729.169 Safari/537.36 " },
            timeout: 7000
        }).then(axiosResponse => {
            console.log('successful axios call!', axiosResponse.status, Date.now() - +(startTime), websites[i]);
        }).catch(error => {
            if (error.response) {
                console.log(' Error getting website from response', websites[i], error.response.status);
                status = error.response.status;
            }
            else if (error.request) {
                console.log(' Error getting website from request', error.request.message, websites[i]);
            }
            else {
                console.log(' Eome other error', error.message);
            }

        }));
    }

    await Promise.all(promises);

    console.log('Finished without blocking calls', Date.now() - +(overallStartTime));
} 
```

这段代码类似于上面的代码，只是它不等待请求完成。它利用`.then`来确保响应在完成时得到处理。

在这个例子中，我使用了一个数组并将承诺存储在其中。因为我想知道我什么时候完全结束，所以我使用`await Promise.all(promises)`。这将阻止我的最后一部分代码，`console.log`执行，直到所有的承诺完成，但它不会阻止循环中其他项目的执行。

[![](img/2305f4b32c03a25e32083a6bde24b7cb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lYTJLcMW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://javascriptwebscrapingguy.com/wp-content/uploads/2019/08/without-blocking-1.png) 

<figcaption>无阻塞，2881 年完成
ms</figcaption>

[![](img/827af137b39bd7a4853478655bba9642.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aezhcFqV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://javascriptwebscrapingguy.com/wp-content/uploads/2019/08/without-blocking-2-1.png) 

<figcaption>无阻塞，3033 ms 完成</figcaption>

[![](img/811469d365b14e542dd881b9f74441f7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0oI5F24n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://javascriptwebscrapingguy.com/wp-content/uploads/2019/08/without-blocking-3-1.png) 

<figcaption>无阻塞，2990 ms 完成</figcaption>

时间差非常显著，在 3 秒无遮挡和 20 秒有遮挡之间。

## 不要 Ddos

[![](img/0d75b87de0625f52685ff99105f599db.png)](https://i.giphy.com/media/3o751RaYn4s9ma4Oly/giphy.gif)

当网络抓取时，有一件事要时刻保持敏感，那就是你给被抓取的网站带来的负担。在这种情况下，我将我的请求分散在许多不同的网站上，因此不会真正担心网站负担过重。

如果你正在抓取一个网站，而没有任何限制或阻止许多请求，你将会非常危险地猛烈抨击。我在这个网站上测试了一下，感受一下它会有多痛。

[![](img/b2de382d34d5641e8ae489457a7cd44a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jzA_5CxO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.imgur.com/d5TJKRR.gif) 

<figcaption>第五十集</figcaption>

上面是我点击这个网站 50 次。你会发现，一旦开始加载，加载时间会显著增加。

[![](img/6c647dc88c7651cbaff4365f30f6f2f2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jZVQjDp5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.imgur.com/PGp5PLl.gif) 

<figcaption>500 命中无阻挡</figcaption>

现在让我们试着击打 500 次。哎哟，看到 502 网关错误了吗？我非常简单地成功地完成了自我 Ddos。在美国，根据计算机欺诈和滥用法案，拒绝服务攻击可能被视为联邦犯罪，处罚包括数年监禁–[维基百科](https://en.wikipedia.org/wiki/Denial-of-service_attack)。哎哟。我们不要那样做。

我并不是说使用阻塞调用是一种适当的速率限制技术，而是说在单个主机上使用非阻塞允许的速度时要小心。

## 寻找商业线索？

使用在 javascriptwebscrapingguy.com 这里谈到的技术，我们已经能够推出一种获得令人敬畏的商业线索的方法。在[了解更多钴情报](https://cobaltintelligence.com)！

帖子 [Jordan 比较了抓取与阻塞和非阻塞调用](https://javascriptwebscrapingguy.com/jordan-compares-scraping-with-blocking-and-non-blocking-calls/)最早出现在 [JavaScript Web 抓取家伙](https://javascriptwebscrapingguy.com)上。