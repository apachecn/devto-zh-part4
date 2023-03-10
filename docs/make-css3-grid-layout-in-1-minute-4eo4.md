# 在 1 分钟内制作 CSS3 网格布局

> 原文：<https://dev.to/vaheqelyan/make-css3-grid-layout-in-1-minute-4eo4>

[![Layout-master logo](img/7805b079292becd07818c2ade05c92a3.png)](https://layout-master.now.sh/)

[网站](https://layout-master.now.sh/)

如果你在使用 CSS3 Grid 时有困难，并且你需要快速写一个布局，那么这个工具可以帮助你。

这个工具的界面非常简单，你可以改变尺寸，改变部件的位置。您可以突出显示网格中的行和列，最终您只是得到生成的 CSS 代码

[![](img/fe3005c4af550df81b73fbf86cdc21d8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--im9Vh3jQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://res.cloudinary.com/dmtrk3yns/image/upload/q_auto/v1566374571/grid-maker/demo.gif)

让我们创建我们的 html 文件，并将已经生成的 css 代码

```
<style>
  /* A little style 💅 */
  .container > div {
    background-color: rgb(211, 234, 252);
    text-align: center;
    padding: 20px 0;
    font-size: 30px;
  }

  .container {
    display: grid;
    grid-template-areas:
      "Header Header Header Header Header"
      "Left Main Main Main Right"
      "Left Main Main Main Right"
      "Left Main Main Main Right"
      "Left Main Main Main Right"
      "Left Main Main Main Right"
      "Footer Footer Footer Footer Footer";
    grid-template-rows: 100px auto auto auto auto auto 100px;
    grid-template-columns: 1fr 1fr 1fr 1fr 1fr;
    grid-gap: 10px;
  }

  .head {
    grid-area: Header;
  }
  .left {
    grid-area: Left;
  }
  .main {
    grid-area: Main;
  }
  .right {
    grid-area: Right;
  }
  .footer {
    grid-area: Footer;
  }
</style>

<div class="container">
  <div class="head"></div>
  <div class="left"></div>
  <div class="main">
    Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod
    tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam,
    quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo
    consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse
    cillum dolore eu fugiat nulla pariatur.......
  </div>
  <div class="right"></div>
  <div class="footer"></div>
</div> 
```

我刚换了`grid-template-rows`

让我们看看结果

[![](img/b7a433958064fddd0d8b29eaf7312f59.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2-Wc29on--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://res.cloudinary.com/dmtrk3yns/image/upload/q_auto/v1566374438/grid-maker/layout-master-demo2.gif)

##### 链接

[网站](https://layout-master.now.sh/)

[回购](https://github.com/vaheqelyan/layout-master)

[@vaheqelyan](https://github.com/vaheqelyan)