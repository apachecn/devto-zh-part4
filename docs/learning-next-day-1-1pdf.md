# 学习 NextJs -第 1 天

> 原文：<https://dev.to/eperedo/learning-next-day-1-1pdf>

为了开始使用 next，您需要安装三个库的最新版本:

1.  然后
2.  反应
3.  反应范围

```
npm install next react react-dom 
```

安装完成后，您将需要一个 package.json 文件。您可以使用 npm init 命令
轻松创建一个

```
npm init -f 
```

**-f** 标志意味着它将使用缺省值，你可以忽略它并使用你想要的值。现在**脚本**部分的 **package.json** 中的
用下一个命令创建一个 **dev** (可以是任何名字)脚本。

```
 "scripts": {
        "dev": "next dev"
    } 
```

**next dev** 将为 next 支持的本地开发生成一个 web 服务器。

### 文件系统 API

下一步将搜索 pages 目录，并转换在您的
应用程序路径上找到的所有文件。

假设您想要一个 About 页面 url (/about)来显示欢迎消息。首先你需要创建一个**页面**
目录

```
mkdir pages 
```

现在，既然你希望 url 是 **/about** ，那么这个文件必须有相同的名字 **About.js** 。因为这里
你在一个 react 安全空间，或者换句话说，你只需要创建一个 react 组件。

```
// pages/About.js
function About() {
  return <h1>Welcome to the About Page!</h1>;
}

export default About; 
```

然后在你的终端上执行 **dev** 脚本

```
npm run dev 
```

就这样，你在浏览器中打开网址[http://localhost:3000/about](http://localhost:3000/about)你应该会看到欢迎信息。

但是对于根 url (/)我们如何为那个页面创建内容呢？简单，在这种情况下 next 将把文件 **index.js** 映射到根 url，聪明吧？

```
// pages/Index.js

function Home() {
  return <h1>Welcome to my App!</h1>;
}

export default Home; 
```

现在转到 [http://localhost:3000](http://localhost:3000) 会显示**欢迎使用我的应用**消息。