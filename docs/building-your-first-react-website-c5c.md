# 建立你的第一个 React 网站

> 原文：<https://dev.to/nexttech/building-your-first-react-website-c5c>

React 是目前最流行的 web 框架之一。多年来，它的受欢迎程度一直在稳步增长，在 [2019 Stack Overflow 开发者调查](https://insights.stackoverflow.com/survey/2019#technology-_-web-frameworks)中首次通过 Angular。

这篇文章将向你展示如何在几分钟内创建你自己的 React 网站。如果你在完成本教程后有兴趣了解更多，请查看我刚刚在 Next Tech 上创建的[开始反应](https://c.next.tech/2EUAVlp)课程，以进一步提高你的反应技能。

现在，让我们开始用 React 构建一个网站吧！

## 先决条件

要完成这些步骤，您需要安装节点程序包管理器(npm)。如果你还没有安装，直接去[https://www.npmjs.com/get-npm](https://www.npmjs.com/get-npm)下载并安装 npm。

> 这也将安装我们将用来运行`create-react-app`的`npx`。

## 创建 React App

创建 React 应用程序是快速启动 React 网站并运行的一个很好的方法。Create React App 是由脸书创建的(React 也是这家公司创建的！).在他们的文件中，他们将其描述为:

> Create React App 是官方支持的创建单页 React 应用程序的方式。它提供了一个没有配置的现代构建设置。

知道 **Create React App 受到 React** 的开发者的支持是一个巨大的加分。让我们用它来开始我们的网站吧！

运行以下命令来创建您的站点:

```
npx create-react-app hello-react 
```

*请注意，该命令可能需要几分钟才能完成。*

## 查看 React 网站

接下来，运行以下命令来启动 React 开发服务器:

```
cd hello-react
npm start 
```

此时，应该会打开一个浏览器选项卡，显示您的 React 站点。如果没有，请在您最喜欢的浏览器中访问 [http://localhost:3000](http://localhost:3000) 来查看您的 React 站点！

## 更新站点

现在，让我们做些改变来更新站点。打开`hello-react/src/App.js`文件，然后替换下面一行:

```
Edit <code>src/App.js</code> and save to reload. 
```

用

```
My first React website! 
```

如果你再次打开网页，你会看到它更新了，而你不必刷新页面！实时重新加载是 Create React App 为您配置的令人惊叹的功能之一。

[![Create React App Website](img/33557a9bd8fba5bbd4b999e8fdd44608.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MRoserLh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pxaicsmec2pheg0jczyx.png)

## 创建 React 组件

接下来，我们将创建一个新的 React 组件。首先，在`src`文件夹中创建一个名为`components`的文件夹。然后在`src/components`文件夹中创建一个名为`HomepageImage.js`的文件。这个文件将保存我们新的主页图片组件。

我们将通过向`HomepageImage.js`文件添加以下代码来创建这个组件:

```
import React from 'react';

function HomepageImage() {
  const url = 'https://cdn.filestackcontent.com/XYrHCaFGRSaq0EPKY1S6';
  return (
    <img src={url} style={{width: 650}} alt='Image of Golden Gate Bridge' />
  );
}

export default HomepageImage; 
```

然后，在`App.js`中，替换

```
<img src={logo} className="App-logo" alt="logo" /> 
```

用

```
<HomepageImage /> 
```

我们还需要通过向文件顶部添加以下代码来导入位于`App.js`顶部的组件:

```
import HomepageImage from './components/HomepageImage' 
```

因为我们移除了 React 徽标的图像，所以您也可以移除该徽标的导入:

```
import logo from './logo.svg'; 
```

最终的`App.js`文件应该是这样的:

```
import React from 'react';
import './App.css';
import HomepageImage from './components/HomepageImage'

function App() {
  return (
    <div className="App">
      <header className="App-header">
        <HomepageImage />
        <p>
          My first React website!
        </p>
        <a
          className="App-link"
          href="https://reactjs.org"
          target="_blank"
          rel="noopener noreferrer"
        >
          Learn React
        </a>
      </header>
    </div>
  );
}

export default App; 
```

现在，在浏览器中再次打开 [http://localhost:3000](http://localhost:3000) 。如果一切正常，您应该会看到以下页面:

[![Image of finished website](img/b7c6f5203908c7299c644dd19b4f938b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GONVyVCU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wutfnkubpj9q1hoqnl1x.png)

祝贺你使用 React 创建了你的第一个网站🎉！

## 下一步

本教程是对使用 React 创建网页的快速介绍。如果你想更好地理解 React，这样你就可以用它来创建很棒的网站，看看我刚刚发布的教授 React 的课程！

你用 React 建立了一个站点吗？欢迎在下面的评论中分享你的网址或者你的项目链接，来炫耀一下吧！

感谢阅读，

安德鲁，软件工程师@ Next Tech

* * *

特别感谢[马丁·范·登·霍维尔](https://unsplash.com/@mvdheuvel?utm_medium=referral&utm_campaign=photographer-credit&utm_content=creditBadge)为这篇文章拍摄了金门大桥的照片！