# 网页抓取 101

> 原文：<https://dev.to/piyukore06/web-scraping-101-g9l>

网络抓取是一个庞大的话题。在这篇文章中，我们将看看它是什么？我们可以在哪里使用它？这是一个基本的例子。

### 什么事？

Web 抓取是 web 开发人员用来从任何给定的网站提取大量数据的一种方法。这主要是为了节省开发人员的时间，以防您想要对任何网站上的大量数据进行计算，这样他们就不必访问这些网站并手动记录所有数据。

网页抓取是指抓取网页并从中提取内容。抓取是下载页面(这是浏览器在您查看页面时所做的事情)。因此，网页抓取是网页抓取的一个主要组成部分，以获取网页供以后处理。一旦取出，就可以进行提取。页面的内容可以被解析、搜索、重新格式化，其数据可以被复制到电子表格或保存到服务器，等等。Web 抓取工具通常会从页面中取出一些内容，在其他地方用于其他目的。

有些网站使用一些方法来防止网页抓取，例如检测和禁止机器人抓取(查看)他们的页面。作为响应，存在依赖于使用 DOM 解析、计算机视觉和自然语言处理中的技术来模拟人类浏览以使得能够收集网页内容用于离线解析的 web 抓取系统。Github 有速率限制机制来控制进出的流量。

### 📜用例？

在这里，当我们学习网络抓取时。有人可能会想，这听起来很酷，但我拿它做什么呢？大多数用例都包括某种自动化。它可能是以下任何一种

*   在线价格监控
*   研究
*   市场分析

*   为机器学习建立大型数据集

*   端到端测试

*   收集房地产列表

*   产品比较网站

当然，不一定要有如此庞大的用例。在网上，你可以找到开发人员变得有创造力的例子，他们通过自动化小事情来帮助他们的日常生活。一位开发人员编写了一个小脚本，用于每天登录并检查她的贷款到期金额，或者当开发人员对 UI 提供的数据表示不满意，需要某种特殊的过滤器时使用。

我们今天的用例是，我们需要一个保存到 JSON 文件的表情列表，包括它的 unicode 和名称(因为谁不喜欢表情)。unicode.org 上有所有表情符号统一代码的官方列表。

注意📝:更新的版本在这里，但是我们想学习抓取，所以我们将坚持使用 html。

### 可以使用的🛠工具

让我们去寻找一个可以帮助我们做到这一点的工具。有两个最常用的 JS 库用于刮 Cheerio 和 Puppeteer。让我们简要地看一下它们中的每一个

### [Cheerio](https://cheerio.js.org/)

Cheerio 是最受欢迎的。根据他们的网站，Cheerio 是专门为服务器设计的快速、灵活、精简的核心 jQuery 实现。像 api 这样的 jQuery 使它成为开发者的宠儿。他们有一个巨大的选择器列表，语法也是从 jQuery 借来的。因为我不太熟悉 jQuery 语法，所以我决定使用 puppeteer。

### [木偶师](//pptr.dev)

Puppeteer 是 headless chrome 的节点 api，Headless chrome 是 Node 推出的一个程序，可以在没有 GUI 的情况下使用它们的浏览器。它通常用于自动化事物，这正是我们所需要的。它使用 [devtool 协议](https://chromedevtools.github.io/devtools-protocol/)。它真的很酷，如果你想看看的话。

木偶戏有事件驱动的架构，这消除了许多潜在的剥落。在木偶剧本中不需要`sleep(1000)`调用。你可以在这里和木偶师[一起玩](https://try-puppeteer.appspot.com/)。因为它是真正的 chromium api，所以比 Cheerio 强大得多。它可以做的事情，如生成 pdf 文件，截图或捕捉时间轴跟踪等等。

### 显示代码

*   安装木偶师

于是开始了一个新项目`npm init -y`
安装木偶师`npm install puppeteer --save`

注意📝:安装后，它会下载一个 Chromium 版本，然后使用`puppeteer-core`驱动它。如果你安装了`puppeteer-core`，它不下载 Chromium。它需要节点版本> > v6.4.0，但是我们下面的例子使用了仅在节点版本> = v7.6.0 中支持的 async/await

*   启动浏览器并导航至网页

```
 const puppeteer = require('puppeteer');

(async () => {
  const browser = await puppeteer.launch({ headless: false });
  const page = await browser.newPage();
  await page.goto('https://unicode.org/emoji/charts/full-emoji-list.html');
  ... 
  await browser.close();
})(); 
```

当你启动木偶师，你得到一个浏览器的实例，它有所有的选项，默认情况下木偶师启动一个无头浏览器，为了调试的目的，你可以设置无头假，然后你可以看到所有的事情将发生与脚本，但请注意，无头模式更快。在结束时，你想关闭浏览器，因为如果你不这样做，你会有内存泄漏，你不希望这样。

*   搜索并获得我们需要的数据

```
 const puppeteer = require('puppeteer');

let scrape = (async () => {
  const browser = await puppeteer.launch({ headless: false });
  const page = await browser.newPage();
  await page.goto('https://unicode.org/emoji/charts/full-emoji-list.html');

  const result = await page.evaluate(() => {
    let data = []
    document.querySelectorAll('table tr').forEach(node => {
      const code = node.querySelector('.code a')
      const name = node.querySelector('.name')
      if (code) {
        data.push({
          code: code.innerHTML.replace('  ', '').split('U+').filter(Boolean).join('_').toLowerCase(),
          name: name.innerHTML
        });
      }
    })
    return data
  });

  await browser.close();
  return result;
});

scrape().then(data => {
    console.log(data) // success
}) 
```

如果传递给`page.evaluate`的函数返回一个承诺，那么`page.evaluate`将等待该承诺被解析并返回其值。
它不是在木偶戏中执行这个功能，而是在 DOM 中执行，所以你可以访问所有的 DOM。我们在文档中搜索所有 emoji unicodes 及其名称，并返回数据。

*   保存日期

```
 const puppeteer = require('puppeteer');
const fa = require('fs');

let scrape = (async () => {
  const browser = await puppeteer.launch({ headless: false });
  const page = await browser.newPage();
  await page.goto('https://unicode.org/emoji/charts/full-emoji-list.html');

  const result = await page.evaluate(() => {
    let data = []
    document.querySelectorAll('table tr').forEach(node => {
      const code = node.querySelector('.code a')
      const name = node.querySelector('.name')
      if (code) {
        data.push({
          code: code.innerHTML.replace('  ', '').split('U+').filter(Boolean).join('_').toLowerCase(),
          name: name.innerHTML
        });
      }
    })
    return data
  });

  await browser.close();
  return result;
});

scrape().then(data => {
  fs.writeFile('emoji-list.json', JSON.stringify(value), 'utf8', () => {
    console.log('DONE!!')
  });
}) 
```

这里我们只是将返回的数据保存到一个 JSON 文件中。这就是你的表情列表。

就是这样！

现在用`node index.js`运行脚本

### 结束注

网络抓取当然是一种有趣的体验。正如我提到的，这是一个广阔的领域，你已经完成了该领域的简短游览。使用`puppeteer`进行抓取可以走得很远。

我希望这篇文章能帮助你开始网络抓取，并且你喜欢它！

如果你有一些问题或意见，请在下面的评论中告诉我，我会回复你。

照片由 Nick Fewings 在 Unsplash 上拍摄