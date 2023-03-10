# 开始下一部分第 1 部分

> 原文：<https://dev.to/wchr/getting-started-with-next-part-1-20m5>

嘿编码器，我在这个[博客](https://wwachira.hashnode.dev/why-you-should-learn-nextjs-cjxm3hlqu001cj4s16jd3pb5s)中写了关于为什么你应该学习 Next 的博客。对于本系列的这一部分，我将介绍一下`next`带来的特性。

> **注意**:您应该已经安装了 NodeJS、npm 和 npx。你要精通 Javascript，有反应。

# 设置我们的项目。

你可以创建自己的回购或克隆我的回购。

```
$ git clone https://github.com/werickblog/react_next_app.git 
```

Enter fullscreen mode Exit fullscreen mode

将目录更改为您的回购本地存放目录

```
$ cd react_next_app 
```

Enter fullscreen mode Exit fullscreen mode

我们现在将使用一个命令
来设置整个下一个应用程序

```
$ npx create-next-app . 
```

Enter fullscreen mode Exit fullscreen mode

启动您的代码编辑器，您应该会看到一个类似下面的目录。

```
 components/ # Holds components that are either reusable or for refactoring
   |- head.js # Component containing head tags such as meta, which contain the metadata of the website
   |- nav.js # Reusable component for the navbar
pages/ # Contains our main components
   |- index.js # Contains component that is rendered when visiting the '/' route
static/ # Contains our static files and media such as CSS, images, and JS
   |- favicon.ico # Our default favicon
  |- .gitignore # Contains a list files and folders that git should ignore
  |- LICENSE # MIT license
  |- next.config.js # Holds configs and next plugins 
  |- package.json # Depedency tracker
  |- README.md # Project doc 
```

Enter fullscreen mode Exit fullscreen mode

在开发模式下运行我们的应用程序。

```
$ npm run dev # For npm 

# or 

$ yarn dev # For yarn 
```

Enter fullscreen mode Exit fullscreen mode

启动浏览器，在端口 3000 上访问本地主机

[![Screenshot 2019-07-09 at 5.23.07 PM.png](img/1f04cc9724c70352372af9b9d0587d2f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ajyEdMG_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.hashnode.com/res/hashnode/image/upload/v1562682199292/YbdSPuxBP.png)

您应该会在浏览器中看到类似上图的内容。

因为我们从头开始构建所有的应用程序，所以让我们删除`pages/index.js`中的所有内容。

省去导入的库。

```
 import React from 'react' // We are writing react so we probably need this
import Link from 'next/link' // the routing library for next
import Head from '../components/head' // component that updates metadata for each page rendered
import Nav from '../components/nav' // reusable navbar component

const Home = () => (
  <div>
      Next meets React
  </div> )

export default Home 
```

Enter fullscreen mode Exit fullscreen mode

让我们探索一下`next`提供的所有特性。

## 路由系统

Next 拥有与`react-router`不同的路由系统，它基于`file-system`。我说`file-system`是什么意思？每当您在`pages/`目录中创建一个文件时，启动您的浏览器并访问带有该文件名称的路径，它将呈现该文件返回的内容。

在 pages 目录下创建一个名为`about.js`
的文件

```
$ cd pages && touch about.js 
```

Enter fullscreen mode Exit fullscreen mode

我们必须渲染一些东西，以便在访问`/about`路线时获得一些东西。

```
import React from "react";
import Link from "next/link";

const About = () => (
    <div>
        Next meets React is a blog series that touches on getting
        started on using Next with React.
    </div> )
export default About; 
```

Enter fullscreen mode Exit fullscreen mode

让我们用链接更新我们的`components/nav`文件，将我们带到“关于”和登录页面(索引)

```
 import React from "react";
import Link from "next/link"; // Next module that helps with routing

const Nav = () => (
  <nav>
    <Link href="/">
      <a>Home</a>
    </Link>
    <Link href="/about">
      <a>About</a>
    </Link>
  </nav> );

export default Nav; 
```

Enter fullscreen mode Exit fullscreen mode

我们应该通过导入`nav`组件
来更新`pages/index.js`和`pages/about.js`文件

```
import React from "react";
import Nav from "../components/nav";

const About = () => (
  <div>
    <Nav />
    <div>
      Next meets React is a blog series that touches on getting started on using
      Next with React.
    </div>
  </div> );

export default About; 
```

Enter fullscreen mode Exit fullscreen mode

运行开发`next`服务器。

```
yarn dev // For yarn 
npm run dev // For npm 
```

Enter fullscreen mode Exit fullscreen mode

当你启动你的浏览器时，你应该有类似的东西

[![Screenshot 2019-07-14 at 6.35.44 PM.png](img/1870d74f4b7006073e47f9c4e9d9138a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TpY7dRKv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.hashnode.com/res/hashnode/image/upload/v1563118584129/G-GxoN8wq.png)

如果您单击“关于”链接，它应该会重定向到“关于”页面。

[![Screenshot 2019-07-14 at 6.37.11 PM.png](img/d564ee8820baec7f1a4b79d111f26532.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--z92OKFbH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.hashnode.com/res/hashnode/image/upload/v1563118656074/h_7o-p5bU.png)

这就是`file-system based routing`的基本含义。没有必要创建一个文件来保存我们所有的路由，我们也没有配置任何东西，我们所做的只是在`pages/`目录中创建一个文件，用我们自己的名字和*噗*，我们有了路由。
[![poof](img/96ec1692b33cfc38be4bd9bf19517e39.png)](https://i.giphy.com/media/Rejrkgm8bVmq8sbAqS/giphy.gif)

## 服务器端渲染

如果你问大多数开发人员，编写自己的代码使你的 SPA 服务器渲染是一场噩梦。`next`自带服务器端渲染。
你可以阅读由[沃尔玛实验室](https://medium.com/walmartlabs/the-benefits-of-server-side-rendering-over-client-side-rendering-5d07ff2cefe8)撰写的这篇文章，文章提到了 SSR 应用相对于 CSR(客户端渲染)应用的所有优势。

## CSS-in-JS

在 react 组件中编写`css`有其优势，例如:

*   组合所有东西，不仅仅是 Javascript，你将不必担心你的 css 会影响另一个组件(例如，消除全局变量)
*   可维护性——当你在一个团队中开发一个产品时，拥有`css-in-js`是一个很大的优势，这样你可以隔离你的 css 而不影响你的队友。您可以毫无问题地使用相似的 css 类名。
*   懒惰——现在我写`css-in-js`是因为它减少了为了改变 css 文件的特定部分而改变编辑器标签或关闭 vim 窗口的麻烦。

让我们试试`css-in-js` `next`功能。目前`next`支持:

*   外部 css，如果你不是一个粉丝

```
 import ".../path/to/your/css; 
```

Enter fullscreen mode Exit fullscreen mode

点击阅读更多相关信息

*   内嵌样式让我们在我们的登陆页面(`pages/index.js`)中尝试一下内嵌样式。

```
 import React from "react"; // We are writing react so we probably need this
   import Link from "next/link"; // the routing library for next
   import Head from "../components/head"; // component that updates metadata for each page rendered
   import Nav from "../components/nav"; // reusable navbar component

   const Home = () => (
       <div>
          <Nav />
          {/* Here we are styling to div, to change the color of our text and aligning the text to the right side */}
         <div style={{ color: "red", textAlign: "right" }}>Next meets React</div>
      </div>
   );

   export default Home; 
```

Enter fullscreen mode Exit fullscreen mode

[![Screenshot 2019-07-24 at 12.15.00 PM.png](img/e47941c7b7fb7ca750c423aa50e9e52c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LxFkzA9x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.hashnode.com/res/hashnode/image/upload/v1563959739153/cv-c0_VGn.png)

*   让我们继续用`css-in-js`来设计我们的登陆页面(`pages/index.js`)

```
 import React from "react"; // We are writing react so we probably need this
    import Link from "next/link"; // the routing library for next
    import Head from "../components/head"; // component that updates metadata for each page rendered
    import Nav from "../components/nav"; // reusable navbar component

    const Home = () => (
        <div className="home-content">
            <Nav />
            {/* Here we are styling to div, to change the color of our text and aligning the text to the right side */}
            <div
      className="home-content__header"
      style={{ color: "red", textAlign: "right" }}
    >
      Next meets React
           </div>
           {/* We have increased the font-size of a div of class name home-content__header */}
           <style jsx>
             {`
                  .home-content__header {
                        font-size: 32pt;
                  }
             `}
             </style>
        </div>
     );

     export default Home; 
```

Enter fullscreen mode Exit fullscreen mode

[![Screenshot 2019-07-24 at 12.29.59 PM.png](img/21dc8b19c9d18951ad7a59a4885e72af.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--azkVd-6T--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.hashnode.com/res/hashnode/image/upload/v1563960617567/LmbndF2An.png)

## 总结

在本文中，我们能够:

*   设置我们的`next`应用程序。
*   了解 NextJS 的主要特性，例如:
    *   按指定路线发送
    *   苏维埃社会主义共和国
    *   CSS-in-JS

## 接下来

*   在本系列的下一部分，我们将升级我们的应用程序，使用一个 [API](https://jsonplaceholder.typicode.com/users) 。

## 加贺

*   演示[链接](https://reactnextapp.ewachira254.now.sh/)
*   回购[环节](https://github.com/werickblog/react_next_app)
*   NextJS 文档[链接](https://nextjs.org/docs)