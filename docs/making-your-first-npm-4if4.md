# 制作您的第一个 npm！

> 原文：<https://dev.to/nfairbairn/making-your-first-npm-4if4>

自从我开始使用 javascript 以来，我们一直在使用这些叫做`npm`的疯狂而神奇的包来为我做事。导入包和获得新的函数，让我在程序中做更酷的事情，这是很棒的，但是作为一名程序员，我不能接受这是魔术。所以我开始寻找更多关于节点包和它们是如何制作的，以及我如何可能制作我自己的。

我在 Flatiron 工作的这段时间里，我们学习了 JavaScript ES6 和 JSX，以便在各种 web 应用程序中使用。我们一直在使用`npm`,但是我们可以使用我们所拥有的相同的代码知识来创建我们自己的包，我们可以分享这些包来制作新的令人兴奋的应用程序。

首先，我们需要设置我们的目录，以包含我们将用于我们的包的所有文件。

```
cd desktop
mkdir my-test-package
cd my-test-package
npm init 
```

它会问你一堆问题，但是在`init`菜单之后，它会提示你选择不同的设置来生成一个`package.json`文件。

在您的`package.json`中，您将指定您的包的名称和描述，并且您还将指定包将从中获取代码的文件。

在名为`main`的文件中会有一个键，它指向你在使用你的包时想要执行的文件。默认设置为`index.js`

既然你的`package.json`知道去哪里找，是时候进入你的`index.js`(或者任何你在`package.json`中设置为主文件的文件)。这里是你为你的包编写方法的地方。

例如，如果我想制作一个包含白板问题解决方案的包，并且我想能够在另一个项目中使用这些方法，该怎么办呢？

在我的`index.js`文件中，我将继续添加我的函数:

```
module.export = (string) => { return string.split('').reverse().join('')} 
```

这个 module.export 方法允许我们将函数作为一个模块导入，这样我们应用程序中的其他文件就可以使用它们的方法，如果它们需要文件路径的话。

现在我们已经完成了我们的包，我们可以创建一个测试应用程序来检查我们的包是否正确导入。

回到主目录，创建一个新项目。

```
npm init -y 
```

y 标志只是让您跳过所有的设置问题。目录创建完成后，我们想要`npm`安装我们新创建的包！

```
npm install my-test-package 
```

由于我们的包没有发布，我们将不得不使用包的绝对文件路径来告诉我们的计算机在哪里检索我们的文件。

```
npm install User/desktop/test-package/npm/my-test-package 
```

现在我们需要将导入的包链接到我们的新应用程序，我们可以通过键入
来完成

```
npm link my-test-package 
```

在我们工作目录的控制台中。

现在我们已经设置好了，打开`app.js`文件，我们就可以导入包在我们当前的应用程序中使用了！

```
const wordReverse = require(‘my-test-package’)
const input = “reverse me!”
const test = wordReverse(input)

console.log(test) 
```

现在运行

```
node app.js 
```

在您当前的终端中，您的输出应该是:

```
=> ‘!em esrever’ 
```

巨大！现在我们可以导入任何我想在我的项目中使用的本地包了！

从这里你可以在 GitHub 上托管它，并且你可以和每个人分享你的方法！或者可以开一个开源库！可能性是无限的！

成功创建包后，您可以将包发布到 npm！之后你可以运行

```
npm install <your-package> 
```

从任何机器，你可以导入你自己的图书馆！

相当整洁！

如果你想了解更多关于节点[的信息，这里](https://nodejs.org/en/docs/)是文档

这里的是通过 npm 文档发布 NPM 的一个很好的指南

你可以在这里创建一个 npm 帐号[来开始发布你所有的令人惊奇的包！](https://www.npmjs.com/signup)