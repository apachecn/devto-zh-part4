# 乔丹和木偶师做代理

> 原文：<https://dev.to/aarmora/jordan-does-proxying-with-puppeteer-20bo>

[演示代码在这里](https://github.com/aarmora/jordan-does-proxying-with-puppeteer)

### 站在巨人的肩上

我在这篇文章中所做的主要是基于[这个 stackoverflow 问题](https://stackoverflow.com/questions/48768959/puppeteer-use-multiple-proxies-and-change-automatic-proxy-if-proxy-refused-conne)。我的主要目标是在我用木偶师抓取网页时能够防止被屏蔽。

我在数字海洋的一个小水滴上做了这个，这样我可以很容易(也很安全？)安装 tor 包。我[使用了 stackoverflow 帖子中引用的指南](https://unix.stackexchange.com/questions/87156/anonymous-url-navigation-in-command-line/87170#87170)来做这件事。

### 进入暗网？

[![](img/f872859058225eddd4424177db08090d.png)](https://i.giphy.com/media/l3c5LjEEiEAcifOW4/giphy.gif)

我承认，在此之前，我对 tor 的唯一了解是来自新闻，所以我对它有点不信任，并有我钻研可怕的黑暗网络的图像。

我做了一点研究，如果我理解正确的话，tor 实际上只是匿名的一种方式。用 tor 包发出请求只允许你使用随机的匿名 IP 地址发出请求。

Tor 也可以用来匿名托管网站，这正是黑暗网络发挥作用的地方。如果你用 tor 托管你的网站(我不知道怎么做),你可以更加隐秘地进行你的活动，并且觉得托管你的网站更加安全。不安全？….主题。

简而言之，安装 tor 包非常简单，我很快就可以用傀儡师运行代理。我发现的另一个很酷的东西是这个用于 vscode 的[远程开发扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.vscode-remote-extensionpack)。我可以用 vscode 连接到我在 digital ocean 上的 linux 机器，并在那里进行所有的代码编辑(和终端工作)。太棒了。

### 代码

下面是来自于 [stackoverflow 帖子](https://stackoverflow.com/questions/48768959/puppeteer-use-multiple-proxies-and-change-automatic-proxy-if-proxy-refused-conne)的一段非常聪明的代码。我发现的问题是`page.on('response', response`查看每一个 http 请求。图像。Javascript。Css。下图显示了回答。

```
 page.on('response', response => {
        console.log('response.status', response.status(), response.request().url());
        if (response.ok() === false) {
            exec('(echo authenticate \'""\'; echo signal newnym; echo quit) | nc localhost 9051', (error, stdout, stderr) => {
                if (stdout.match(/250/g).length === 3) {
                    console.log('Success: The IP Address has been changed.');
                } else {
                    console.log('Error: A problem occured while attempting to change the IP Address.');
                }
            });
        } else {
            console.log('Success: The Page Response was successful (no need to change the IP Address).');
        }
    }); 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/60aa642f4d847daeecfdf0c23a18baf4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uuLPQHyC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://javascriptwebscrapingguy.com/wp-content/uploads/2019/08/image-1.png)

检查所有这些的问题是，很可能是一个图像 404 或一些 css 文件，然后我们重置我们的 IP 地址。有时候，仅仅是一个请求，我就要重置 3 到 4 次我的 IP 地址。我不喜欢这样，它减慢了速度。

[![](img/fa4cfe3a50f708f3c868a8c7a12a5fb0.png)](https://i.giphy.com/media/gGeyr3WepujbGn7khx/giphy.gif)

我做了以下调整，这让事情感觉好多了。我只是添加了一个检查，以确保我们只有在从执行的显式请求中获得错误状态(我定义为 400 或更多)时才可能轮换 IP 地址。

```
 page.on('response', response => {
        // Ignore requests that aren't the one we are explicitly doing
        if (response.request().url() === url) {
            if (response.status() > 399) {
                console.log('response.status', response.status(), response.request().url());
                exec('(echo authenticate \'""\'; echo signal newnym; echo quit) | nc localhost 9051', (error, stdout, stderr) => {
                    if (stdout.match(/250/g).length === 3) {
                        console.log('Success: The IP Address has been changed.');
                    } else {
                        console.log('Error: A problem occured while attempting to change the IP Address.');
                    }
                });
            }
            else {
                console.log('Success: The Page Response was successful (no need to change the IP Address).');
            }
        }
    }); 
```

Enter fullscreen mode Exit fullscreen mode

我不是 100%喜欢检查那些状态代码。如果 404 是合法的 404 呢？我认为这不值得改变 IP。能够更好地检测主动阻塞尝试将会更好。例如，谷歌会把你重定向到一个 recaptcha 页面。我真的很努力地试图得到它的截图，但我不能让他们重定向我，我打它相当努力。谷歌似乎对这种事情采取了温和的方式。如果你不狠狠地打击他们，他们几乎不在乎。我看到 recaptcha 页面出现，然后我再次尝试我的原始页面，它的工作。

**更新:获得验证码页面**

[![](img/e546afd44a6a6526c43becd7f9219957.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0eg1fby3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://javascriptwebscrapingguy.com/wp-content/uploads/2019/08/image-4.png)

所以我结束了添加一段寻找验证码形式的代码。如果它得到了它，它就调用这个函数，这个函数会给它一个新的 IP 地址。

```
 await page.goto(url);
    try {
        await page.waitForSelector('#captcha-form', { timeout: 2500 });
        console.log('captcha time, let us proxy');
        await browser.close();

        exec('(echo authenticate \'""\'; echo signal newnym; echo quit) | nc localhost 9051', (error, stdout, stderr) => {
            if (stdout.match(/250/g).length === 3) {
                console.log('Success: The IP Address has been changed.');
            } else {
                console.log('Error: A problem occured while attempting to change the IP Address.');
            }
        });
        return getTheStuff();
    }
    catch (e) {
        console.log('no captcha, carry on!');
    } 
```

Enter fullscreen mode Exit fullscreen mode

### 多快？

[![](img/91e81cf1ffc03a591e8ba8e243aad4cc.png)](https://i.giphy.com/media/UzK6dKKtm4S8o/giphy.gif)

接下来要检查的是有代理和没有代理的速度。我要去的网址是`const url = 'https://www.google.com/search?q=bird+food';`。我的函数是这样的:

```
 for (let i = 0; i < 10; i++) {
        const start = new Date();
        await getTheStuff();
        console.log(`Completed ${i} in ${+(new Date()) - +(start)}`);
    } 
```

Enter fullscreen mode Exit fullscreen mode

代理打开时的时间？在 10 次尝试中，最快时间是 9219 毫秒。那相当慢。

[![](img/d96718304f15a16b0d4409fcca80256e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--u2HaoCaV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://javascriptwebscrapingguy.com/wp-content/uploads/2019/08/image-2.png)

没有代理？10 次尝试中最快的是 5550 毫秒，所有的尝试都要快得多。

[![](img/aaade56db02bcb6e282b8ac3e6eb263e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6WTmi57Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://javascriptwebscrapingguy.com/wp-content/uploads/2019/08/image-3.png)

简而言之，使用代理花费的时间是原来的两倍。这非常重要。因此，虽然我认为这是非常好的知识，但如果我在做任何类型的谷歌抓取时看到我得到了一个验证码，我可能会重试我的 URL。

结束了。

[演示代码在这里](https://github.com/aarmora/jordan-does-proxying-with-puppeteer)

小 PS。我的许多特色图片来自 Unsplash.com。这是一个非常好的资源。今天的来自[雅各布·欧文斯](http://www.directorjakobowens.com/?utm_medium=referral&utm_source=javascriptwebscrapingguy.com-love-your-picture-man-thanks)。谢谢雅各布！

## 寻找商业线索？

使用在 javascriptwebscrapingguy.com 这里谈到的技术，我们已经能够推出一种获得令人敬畏的商业线索的方法。在[了解更多钴情报](https://cobaltintelligence.com)！

帖子[乔丹与木偶师](https://javascriptwebscrapingguy.com/jordan-does-proxying-with-puppeteer/)做代理首先出现在[的 JavaScript 网页抓取工具](https://javascriptwebscrapingguy.com)上。