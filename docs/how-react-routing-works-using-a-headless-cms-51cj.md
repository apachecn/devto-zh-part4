# React 路由如何使用无头 CMS 工作

> 原文：<https://dev.to/agilitycms/how-react-routing-works-using-a-headless-cms-51cj>

Agility CMS 的独特之处在于它为页面路由提供了 REST API。它实际上并不为你做任何路由选择——它只是为你提供了一个代表站点地图的路由表对象和代表每个页面的页面对象。

作为开发人员，这使您能够将复杂站点地图的设置工作交给内容团队。这可以节省大量的时间，使得使用 CMS 开发网站更快。

在指南[为什么 Agility CMS 具有页面管理功能](https://agilitycms.com/resources/guide/why-agility-cms-has-page-management-as-a-headless-cms)中，我概述了如何使用页面模板和模块在 CMS 中管理页面。

让我们看看它在 React 站点中是如何工作的。

## 以代码开始

首先，[开始使用 Agility CMS React 应用程序](https://help.agilitycms.com/hc/en-us/articles/360031121692)。这就像克隆一个 GitHub repo 并运行一个命令行来开始一样简单。哦，你也应该[注册一个免费的敏捷 CMS 账户](https://account.agilitycms.com/sign-up)。

### 1:克隆回购:

```
git clone https://github.com/agility/agility-create-react-app 
```

Enter fullscreen mode Exit fullscreen mode

### 2:安装 npm 依赖项:

```
npm install 
```

Enter fullscreen mode Exit fullscreen mode

### 3:本地启动站点:

```
npm start 
```

Enter fullscreen mode Exit fullscreen mode

## 路由是如何工作的？

这一切都从 sitemap 对象开始，Agility CMS 通过 REST API 给了我们这个对象。

它看起来像这样(为了清楚起见，我简化了一点):

```
{
    "/home": {
        "title": "Home",
        "name": "home",
        "pageID": 2
    },
    "/posts": {
        "title": "Posts",
        "name": "posts",
        "pageID": 3
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

站点中的每条路线都可以从该对象访问，因此您可以快速查找路线是否存在。

下面是我们如何在 React 中使用它。

```
async routePage() {
    const api = this.props.agility.client;
    try {
        //get the sitemap route table
        const sitemap = await this.getSitemap(api);

        //get the path from the browser
        const path = document.location.pathname.toLowerCase();
        const pageInSitemap = sitemap[path];

        //if we are on the homepage, get the first route
        if (path === '/') {
            const firstPagePathInSitemap = Object.keys(sitemap)[0];
            pageInSitemap = sitemap[firstPagePathInSitemap];
        }

        //only proceed if this path is in the table
        if (pageInSitemap) {

            //get the actual page object
            const page = await api.getPage({
                pageID: pageInSitemap.pageID,
                languageCode: this.props.agility.config.languageCode
            });

            //set this page in our state object
            this.setPage(page, pageInSitemap);

        } else {
            //Could not find page
            this.pageNotFound();
        }

    } catch (error) {
        //Throw error
        this.handleError('error getting sitemap :(', error);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们首先加载站点地图，然后使用当前的 location.pathname 来检查当前页面是否是一个可用的路径。如果我们当前的路径是“/”，那么我们使用站点地图的第一页。

现在，我们使用名为 **pageInSitemap** 路由对象，并使用 **pageID** 调用 **getPage()** 。

一旦我们有了页面对象，Aglity CMS 就会给我们实际呈现这个页面所需的所有数据。

在我们名为 **agility.config.js** 的文件中，我们设置了哪些 React 组件将为每个**页面模板**和在任何可用页面对象上配置的**模块定义**呈现(这都是之前在 CMS 中设置的)。

```
//Our Agility Modules
import RichTextArea from './modules/RichTextArea'
import Jumbotron from './modules/Jumbotron'

//Our Agility PageTemplates
import OneColumnTemplate from './pageTemplates/OneColumnTemplate'

export default {
    guid: '...', //Set your guid here
    fetchAPIKey: '...', //Set your fetch apikey here
    previewAPIKey: '...', //set your preview apikey
    languageCode: 'en-us',
    channelName: 'website',
    isPreview: true,
    moduleComponents: {
        RichTextArea,
        Jumbotron
    },
    pageTemplateComponents: {
        OneColumnTemplate
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们有 2 个页面模板设置，以及 4 个不同的模块。每个组件都将由一个不同的组件呈现，我们在顶部指定了 import 语句，每个组件都将被交付一个 **props** 变量，该变量包含它需要的来自 Agility CMS 的所有数据。

## 页面模板组件

让我们看看呈现页面模板的 OneColumnTemplate 组件。

```
import React, { Component } from 'react';
import { ContentZone } from '../agility-react'

class OneColumnTemplate extends Component {
    render() {    
        return (
        <div className="one-column-template">
            <ContentZone name='MainContentZone' {...this.props} />
        </div>
        );
    }
}

export default OneColumnTemplate; 
```

Enter fullscreen mode Exit fullscreen mode

这非常简单——我们在这里所做的就是删除一个带有 name 属性的**组件，该属性与页面模板中定义的区域相匹配。我们还将讨论道具——我们将看到当我们渲染模块组件时，道具是多么重要。ContentZone 组件现在将查看**页面**对象，以呈现由我们的内容编辑器拖放到其上的模块。**

 **## 模块组件

Agility CMS 中的每个模块都可以有自己的属性。这些作为道具传递到我们的组件中。这是我们的超大屏幕组件，这是一个呈现标题和子标题的简单例子:

```
import React, { Component } from 'react';

import './Jumbotron.css'

class Jumbotron extends Component {
    render() {    
        return (
            <section className="jumbotron">
                <h1>{this.props.item.fields.title}</h1>
                <h2>{this.props.item.fields.subTitle}</h2>
            </section>
        );
    }
}

export default Jumbotron; 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，将**标题**和**副标题**属性输出到标题标签中是一件简单的事情。这些已在 Agility CMS 中设置为模块属性。当您向具有不同内容类型的模块添加更多属性时，它们就可以作为道具使用了。

## 还有一件事...

有一个非常重要的部分我还没有谈到:react-router 组件，它允许我们在路由改变时只改变每个页面的主要部分。

看看我们的 App.js 文件:

```
import React, { Component } from 'react';
import { Switch, Route } from 'react-router-dom'

import './App.css';

//The Agility Router
import { PageRouter } from './agility-react'

//Shared Components
import GlobalHeader from './GlobalHeader'

class App extends Component {
  componentDidMount() {

  }

  render() {
    return (
      <div className="App">
        <GlobalHeader agility={this.props.agility} />
        <main className="main">
          <Switch>
            <Route path="*" render={() => <PageRouter agility={this.props.agility} />} />
          </Switch>
        </main>
      </div>
    );
  }
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

**和**组件由 react-router 控制，我们有一个组件触发我上面描述的所有逻辑。****

 ****这是一个非常简单的例子，说明了我们如何在带有 Agility CMS 页面的 React 应用程序中执行路由。

我认为这是一种魔法，当你把所有这些都准备好，允许你的内容编辑创建各种页面，并把模块放在他们喜欢的地方。它把你作为一个开发者的工作和它相乘。

这也是敏捷 CMS 成为最快 CMS 的原因之一！******