# 用于积极开发的 5 个 chrome 扩展

> 原文：<https://dev.to/aduranil/5-chrome-extensions-for-active-development-1k0j>

### 1。[灯塔](https://chrome.google.com/webstore/detail/lighthouse/blipmdconlkpinefehnmjammfjpmpbjk?hl=en) -对你的网站进行全面审计

Lighthouse 从 5 个方面对你的网站进行全面审核:性能、可访问性、最佳实践、SEO，以及你是否有一个进步的 Web 应用。每个类别都细分为几个指标，并包含改进和诊断的机会。

点击生成报告:
[![](img/831f95368f3328ad977374c7a97f145d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mZ-0nSa0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uorzall0bd8znkyp5dul.png)

生成这个(注意`localhost`会有较慢的性能指标):
[![](img/d83b8189dfe6c41f6d6ac42805e86d76.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QYvfaccq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wnfhrpt7fbci1ofmqea5.png)

### 2。 [stylebot](https://chrome.google.com/webstore/detail/stylebot/oiaejidbmkiecgbjeifoejpgmdaleoha) -改变网站的外观(去掉 github prs 上烦人的红色高亮)

这对于我来说很有用，可以覆盖 github pr 中每次使用撇号时出现的红色突出显示，但 github 有时会认为这是语法错误。要覆盖语法，

转到样式部分的扩展:
[![](img/4d4a22b5d40a627f230cf12132a9cc59.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yQp4_oEK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/z5u0cdb97k9dm0yxhbb8.png)

用下面的 css 创建一个新样式:
[![](img/27917a838f26f6edc383a51cb055c82e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_quGnSK7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/e24ug46m87w5l5omcc3y.png)

### 3。 [jsonformatter](https://chrome.google.com/webstore/detail/json-formatter/bcjindcccaagfpapjjmafapmmgkkhgoa) -让 json 可读

这使得阅读 json 更加容易，也让您可以折叠 json 中的树。如果你愿意，也可以切换回“原始”json

无语法高亮:
[![](img/4be2060dfcad1e92ce70f714524f8edd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ywq7UW_D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ld6q8g74otws9za9pln1.png)

带语法高亮:
[![](img/6827629f0b0559cba09e989b71d1b7f0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---RskCQBT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jgmrk3ntsb55ukmszsf2.png)

### 4。 [react 开发者工具](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi)——检查你的 React 组件，并查看不同站点运行的 React 版本

如果你使用 React，这是一个很好的选择。我不怎么用这个，但它可能很有用。“Profiler”选项卡帮助您提高应用程序的[性能](https://reactjs.org/blog/2018/09/10/introducing-the-react-profiler.html#deep-dive-video)。如下面的截图所示，它分解了你所有的组件，然后突出显示它们，在右边显示所有的道具:
[![](img/b9e714185717d72b8104a58aa45b68a8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--z4tZaw2K--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/j44wbnnaujtjil40btap.png)

您还可以查看不同站点运行的 React 版本，或者他们是否在使用 React，并通过单击 inspect 打开`React`选项卡。这里以脸书为例:
[![](img/b4ea0368e6509ba8216ec04897f3caed.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--W6vTB-9s--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kzrahwoz9u3nzs7c79pa.png)

### 5。 [Redux DevTools](https://chrome.google.com/webstore/detail/redux-devtools/lmhkpmbekcpmknklioeibfkpmmfibljd) -查看商店里有什么，并检查状态变化

这是我用得最多的工具。如果使用 Redux，这是绝对必须的。这让您可以看到商店里有什么，也可以看到商店随着不同行动的分派而更新:

[![](img/2fc2e52f50ce14234100f266b6d0b938.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hUiiPqN3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qkrjc9z7h6gabm0eaj4r.png)