# Node.js 中的环境变量.正确的方式！

> 原文：<https://dev.to/numtostr/environment-variables-in-node-js-the-right-way-15ad>

这是我的第一篇博文。希望你们都喜欢🤞。

环境变量是使用 Node.js 或任何服务器端语言进行开发的非常基础的部分。它们总是包含非常敏感的数据，不打算与外界共享。您必须确保您的服务器得到正确配置，以便在开发和生产环境中使用正确的变量。任何错误都可能导致你的服务器崩溃。

在 Node.js 中使用环境变量非常容易和简单。这篇文章将带您了解在 Node.js 中使用环境变量的不同方法。

如果你想了解 Node.js 中的环境变量，点击[这里](https://nodejs.org/docs/latest/api/process.html#process_process_env)。

## 1。使用 package.json

你是怎么想的？但是您可以在 npm 脚本的*包. json* 中直接传递`key=value`对。这是一个放置环境变量的有效位置，但是 ***不是一个安全的*** 位置。

下面是在 npm 的启动脚本中将 Node.js 执行环境设置为生产环境的示例。

注意:这可能无法在 Windows 操作系统上运行。

```
{  ...  "scripts":  {  "start":  "NODE_ENV=production node bin/www.js"  }  ...  } 
```

Enter fullscreen mode Exit fullscreen mode

当我使用 [debug](https://github.com/visionmedia/debug) 模块或设置 Node.js 执行环境时，我也使用这种方法。使用这种方法时需要考虑以下几点:

*   不应该不把任何敏感数据放在这里，因为它对每个人都是可见的，你不能忽略*中的 *package.json* 。gitignore* 。
*   不要放两个以上的变量，否则会很快变得一团糟。
*   分离开发和生产环境的变量可能非常困难。

## 2。不同的密钥文件

这种方法完全不同于第一种方法，并且解决了第一种方法的一些问题。

不使用 *package.json* ，我们可以使用 **keys.dev.js** 进行开发，使用 **keys.prod.js** 进行生产环境。每个文件为不同的环境存储不同的变量。

* * *

***确保忽略 keys.dev.js 在。gitignore，然后提交任何更改。*T3】**

* * *

但是我们如何使用它们呢？

使用它们可能很棘手(当我最初感到沮丧时)，所以要注意。两个文件都由第三个文件导出，即 **keys.js** ，该文件检查 Node.js 执行环境，即`NODE_ENV`，并导出正确的 keys 文件。

但是在运行我们的代码之前，如何检查 Node.js 环境呢？查看[第一个方法](#using-package-json)以获得简要说明。下面是这种方法的一个例子:

```
// keys.dev.js ==========
module.exports = {
    PORT: 5000,
};

// keys.prod.js ==========
module.exports = {
    PORT: process.env.PORT,
};

// keys.js ==========
const devKeys = require("keys.dev.js");
const prodKeys = require("keys.prod.js");

if (process.env.NODE_ENV === "production") {
    module.exports = prodKeys;
} else {
    module.exports = devKeys;
} 
```

Enter fullscreen mode Exit fullscreen mode

这种方法解决了第一种方法的所有问题。

*   可以轻松管理多个变量。
*   开发和生产都有各自的关键。
*   在*中可以忽略开发键，即 **keys.dev.js** 。对他人保守秘密。*

但是没有人愿意维护额外的代码/文件(包括我)。一定有更好的办法做到这一点！

## 3。`.env`前来救援

。env 是一个特殊的文件，用于定义 Node.js 中的环境变量。它保存了用于定义变量的`key=value`对。

* * *

***确保忽略。env 在。gitignore，然后提交任何更改。*T3】**

* * *

但是，Node.js 不知道如何读取和解析这个文件。我们如何做到这一点？您可以编写自己的逻辑来读取和解析文件，或者使用第三方模块来完成繁重的工作。

一个流行的模块是 [dotenv](https://github.com/motdotla/dotenv) (我用的)，它可以引导基本的。环境文件。

#### ~创建文件

首先创建一个名为。项目的*根中的 env，它包含 dotenv 将在环境中注入的所有变量。* 

```
# .env ======
PORT=5000
WHO_AM_I="Who Knows" 
```

Enter fullscreen mode Exit fullscreen mode

#### ~配置 dotenv

首先从 npm 安装 dotenv 包作为开发依赖项，因为我们在生产中不需要它。

```
npm i -D dotenv 
```

Enter fullscreen mode Exit fullscreen mode

加载 dotenv 包有几种方法。但是，我会告诉你我喜欢的方法。

加载 dotenv 包并正确读取。env 文件你要修改 package.json 中的脚本.如下图

```
{  ...  "scripts":  {  "start":  "node bin/www.js",  "dev":  "node -r dotenv/config bin/www.js"  //  For  nodemon  users  ====  //  "dev":  "nodemon -r dotenv/config bin/www.js"  }  ...  } 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，有两个脚本

*   **开始**生产
*   **dev** 用于加载 dotenv 模块的开发

这将确保我们不会在生产中意外加载 dotenv。

#### ~运行代码

现在，您可以运行服务器，但需要键入以下命令。

```
npm run dev 
```

Enter fullscreen mode Exit fullscreen mode

然后嘣！现在，您可以使用。env 文件，使用以下语法。

```
process.env.YOUR_VARIABLE_NAME; 
```

Enter fullscreen mode Exit fullscreen mode

这是什么魔法？这是怎么回事？在开发脚本中，我们告诉节点通过传递`-r <module_name>`标志来预加载一个模块。通过要求 dotenv/config 来读取和解析。env 并在环境中设置变量，并在运行我们的代码之前提供对这些变量的访问。

现在，我们有一个单一的地方来定义所有的环境变量。

为了让生活变得简单一点，你可以创建一个单独的文件，即 **keys.js** ，它导出所有这些变量，就像我们在第二种方法中所做的那样。这有助于我们组织代码中使用的所有变量。

**注意:**如果你在。env 然后也在 **keys.js** 文件中更新你的导出。

```
// keys.js ======
module.exports = {
    PORT: process.env.PORT,
    WHO_AM_I: process.env.WHO_AM_I,
}; 
```

Enter fullscreen mode Exit fullscreen mode

### 需要考虑的要点

*   总是忽略你在*中的发展关键。gitignore* 。
*   不要乱用`NODE_ENV`变量。除了**开发**或**生产**之外的值会破坏你的应用。
*   更改环境变量后，请务必重启应用程序。