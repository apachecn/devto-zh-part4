# 用节点抓取网站

> 原文：<https://dev.to/cpow/scraping-websites-with-nodejs-11om>

我目前正在做一个兼职项目，我想在某些页面上收集和存储博客帖子。对于这个项目，我选择使用 NodeJS。我最近更多地使用 javascript，所以我认为用 Node 代替 Ruby、Python 或其他什么会很有趣。

## 明面上

当抓取带有节点的网站时，有两个非常好的工具可以使用: [Axios](https://github.com/axios/axios) 和 [Cheerio](https://github.com/cheeriojs/cheerio)

结合使用这两个工具，我们可以获取网页的 HTML，将其加载到 Cheerio(稍后将详细介绍)，并查询元素以获取我们需要的信息。

### Axios

Axios 是一个基于承诺的 HTTP 客户端，适用于浏览器和 NodeJS。这是一个众所周知的包，被用在无数的项目中。我参与的大多数 React 和 Ember 项目都使用 Axios 进行 API 调用。

我们可以用 axios 获取一个网站的 HTML:

```
 import axios from 'axios';

  await axios.get('https://www.realtor.com/news/real-estate-news/'); 
```

☝️会给我们我们请求的网址的 HTML。

### 加油

Cheerio 是我迄今为止从未听说过的最棒的套餐。实际上，Cheerio 为您提供了对所加载的 HTML 的 DOM 结构的类似 jQuery 的查询！它很神奇，可以让你做这样的事情:

```
 const cheerio = require('cheerio')
  const $ = cheerio.load('<h2 class="title">Hello world</h2>')

  const titleText = $('h2.title').text(); 
```

如果您对 JS 开发非常熟悉，您应该对此非常熟悉。

### 最终脚本

有了 Axios 和 Cheerio，制作我们的 NodeJS scraper 非常简单。我们用 axios 调用一个 URL，并将输出的 HTML 加载到 cheerio 中。一旦我们的 HTML 被加载到 cheerio 中，我们就可以在 DOM 中查询我们想要的任何信息！

```
import axios from 'axios';
import cheerio from 'cheerio';

export async function scrapeRealtor() {
  const html = await axios.get('https://www.realtor.com/news/real-estate-news/');
  const $ = await cheerio.load(html.data);
  let data = [];

  $('.site-main article').each((i, elem) => {
    if (i <= 3) {
      data.push({
        image: $(elem).find('img.wp-post-image').attr('src'),
        title: $(elem).find('h2.entry-title').text(),
        excerpt: $(elem).find('p.hide_xxs').text().trim(),
        link: $(elem).find('h2.entry-title a').attr('href')
      })
    }
  });

  console.log(data);
} 
```

### 输出

我们现在有我们的报废信息！

```
[ { image:
     'https://rdcnewsadvice.wpengine.com/wp-content/uploads/2019/08/iStock-172488314-832x468.jpg',
    title:
     'One-Third of Mortgage Borrowers Are Missing This Opportunity to Save $2,000',
    excerpt:
     'Consumer advocates have an important recommendation for first-time buyers to take advantage of an opportunity to save on housing costs.',
    link:
     'https://www.realtor.com/news/real-estate-news/one-third-of-mortgage-borrowers-are-missing-this-opportunity-to-save-2000/' },
  { image:
     'https://rdcnewsadvice.wpengine.com/wp-content/uploads/2019/08/iStock-165493611-832x468.jpg',
    title:
     'Trump Administration Reducing the Size of Loans People Can Get Through FHA Cash-Out Refinancing',
    excerpt:
     'Cash-out refinances have grown in popularity in recent years in tandem with ballooning home values across much of the country.',
    link:
     'https://www.realtor.com/news/real-estate-news/trump-administration-reducing-the-size-of-loans-people-can-get-through-fha-cash-out-refinancing/' },
  { image:
     'https://rdcnewsadvice.wpengine.com/wp-content/uploads/2019/08/GettyImages-450777069-832x468.jpg',
    title: 'Mortgage Rates Steady as Fed Weighs Further Cuts',
    excerpt:
     'Mortgage rates stayed steady a day after the Federal Reserve made its first interest-rate reduction in a decade, and as it considers more.',
    link:
     'https://www.realtor.com/news/real-estate-news/mortgage-rates-steady-as-fed-weighs-further-cuts/' },
  { image:
     'https://rdcnewsadvice.wpengine.com/wp-content/uploads/2019/07/GettyImages-474822391-832x468.jpg',
    title: 'Mortgage Rates Were Falling Before Fed Signaled Rate Cut',
    excerpt:
     'The Federal Reserve is prepared to cut interest rates this week for the first time since 2008, but the biggest source of debt for U.S. consumers—mortgages—has been getting cheaper since late last year.',
    link:
     'https://www.realtor.com/news/real-estate-news/mortgage-rates-were-falling-before-fed-signaled-rate-cut/' } ] 
```