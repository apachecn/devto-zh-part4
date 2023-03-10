# 如何创建和发布 NPM 包

> 原文：<https://dev.to/perlatsp/how-to-create-and-publish-npm-packages-54p3>

这篇文章旨在学习什么是 npm，我们需要什么来运行/安装 npm 包，我们将学习如何创建一个包并将其发布到 npm 注册表中，并感觉自己像一个酷而厉害的开发人员😎。

### 目录

1.  [什么是 NPM](#what-is-npm)
2.  [要求](#requirements)
3.  [我们的 NPM 套餐](#our-package)

### 什么是 NPM

什么是 npm 来着？嗯，NPM 代表 **N** 颂 **P** 包装 **M** 管理者，正如作者在他们的[网站](https://www.npmjs.com/about)上定义的那样:

*   npm 是 [Node.js](http://nodejs.org/) 的包管理器。它创建于 2009 年，作为一个[开源项目](https://github.com/npm/npm)，帮助 JavaScript 开发者轻松共享打包的代码模块。
*   npm 注册表是 Node.js、[前端 web 应用](http://www.ember-cli.com/)、[移动应用](http://cordova.apache.org/)、[机器人](https://tessel.io/)、[路由器](https://linerate.f5.com/)以及 JavaScript 社区无数其他需求的开源代码包的公共集合。
*   npm 是允许开发人员安装和发布这些包的命令行客户端。

### 我们的套餐

就本文而言，我们将一步一步地了解创建 npm 包的过程，并将其发布到 npm 注册中心，以便世界上的其他开发人员可以使用我们的包。

我们要创建的包是一个简单的 CLI 应用程序，它将接受一封电子邮件作为参数，并检查该电子邮件是否在公共数据泄露中被发现。为了实现这一点，我们将向外部 API(haveibeenpawned.com 网站的 API)发出 HTTP 请求。我们包裹的名字？**典当行**🙄 😉

### 要求

要构建我们的包，我们需要以下内容

*   一台工作的计算机💻 🤓我将使用 MacOS，但过程与所有操作系统相似。
*   代码编辑器📜(我用的是 [Visual Studio 代码](https://code.visualstudio.com/)或者 [WebStorm](https://www.jetbrains.com/webstorm/)
*   [NodeJS](https://nodejs.org/) 用 NPM 安装了我们的系统
*   互联网连接

### 入门

### 安装 NodeJS

要在我们的系统上安装 NodeJS，我们可以用很多方法，我将演示其中的两种方法。

### 从官网安装

访问 NodeJs 官方网站[https://nodejs.org](https://nodejs.org/)，按下下载按钮

[![img](img/f4a04e75e1d6cd26bb0c5bb80e648f04.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HcqpuVH---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/0%2AZtGv4qJEsgLmCNbj.png)

下载完成后，打开文件并按照说明完成安装。这样做将安装节点和 npm。

### 使用自制软件安装

[Homebrew](https://brew.sh/) 是一个用于 MacOS 或 Linux 的包管理器。

首先，我们需要安装家酿(如果还没有安装的话)。

打开终端并运行以下命令:

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)" 
```

这将为我们下载和安装家酿。

然后运行`brew update`以确保 Homebrew 是最新的。

要安装节点，我们只需运行下面的命令:

```
brew install node 
```

要确认我们已经在系统上安装了节点和 NPM，我们可以运行以下命令:

`node -v`和`npm -v`它将显示我们刚刚安装的版本。

[![img](img/96d03aa703a0e6332b458cd6d314e224.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3P_nxMfw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/0%2AKYB6wuOfDJXYBagW.png)

### 创建包

首先，我们需要为我们的项目创建一个目录。我喜欢把我所有的项目放在🗂.的目录里打开终端并运行`mkdir pawnhub`这将创建一个名为 *pawnhub* 的文件夹，然后进入目录`cd pawnhub`。

### NPM 初始化

现在我们在项目目录中，我们需要初始化一个 npm 项目。我们可以通过运行`npm init`来实现。这将启动交互式剪辑，并询问我们一些关于新包的信息，如名称、版本、描述、作者和许可证。

[![img](img/d9d97301624f1b4623cfa6cbb003c786.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--s6Sa2FZc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/0%2AeNQ5CF47TmtTlWln.png)

当我们填写所有信息时，系统会要求我们确认刚刚输入的信息。

[![img](img/7793823a8055df8112ebc9c9a523ef26.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gXNQOX0V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/0%2AZWAH3ZD9p6w1EfCF.png)

接下来，我们需要在代码编辑器中打开项目。使用 Visual Studio 代码，我们可以运行命令`code .`打开当前目录，或者我们可以打开 Visual Studio 代码，然后按 COMMAND+O 打开目录。

你会发现我们只有 1 个文件`package.json`。更多关于 package.json 文件[在这里！](https://docs.npmjs.com/files/package.json)

[![img](img/c23976e4361270e7d42ebe567a1848f6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kXnFMa_Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/0%2ABqtAfDm0pfkrsY0z.png)

### 主文件

我们的下一步是创建我们将要使用的主文件，即`index.js`文件。

在我们的根目录`touch index.js`中创建文件。打开文件并复制粘贴以下内容:

```
let message = 'Hello World!';console.log(message) 
```

运行该文件非常简单。打开终端并运行`node index.js`这将告诉 Node 运行 index.js 文件。我们应该在终端上看到以下内容。

[![img](img/e45b164b267042700829a4b838bd24b2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QOIII1Pi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/0%2AF4n7X9-1w6q3M0nK.png)

很酷吧？但是，它不是动态的。我们不能改变输出的信息！我们将在下一节学习如何使用参数来实现这一点。

### 节点参数

通常，当我们想要传递参数时，我们做以下事情:

但问题是，你如何获得这些论据？

在 Node.js 中检索参数最简单的方法是通过`process.argv`数组。这是一个全局对象，我们无需导入任何额外的库就可以使用它。这些参数可以通过`process.argv`数组在应用程序中访问。让我们开始使用论据！

将文件修改如下:

```
let message = 'Hello World!';console.log(process.argv) 
```

然后运行`node index.js argument`我们将得到类似下图的东西。

[![img](img/f8abf6d2d96c800d3732971b6f239a02.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VV6Tq_mD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/0%2AhJF_HQkeWtkW-TcA.png)

你会注意到我们有一个包含 3 个字符串项的数组。我用 0，1 和 2 做了标记。

带键`0`的参数是`node`本身，参数`1`是正在执行的文件，最后一个(`2`)参数是我们将在本教程中使用的参数。

好，现在我们需要对最后一个论点做些什么。让我们在控制台上显示一条定制的消息。

将代码更改如下:

```
let name = process.argv[2];let message = `Hello ${name}`;console.log(process.argv) 
```

我们在这里做的是初始化一个名为`name`的变量，它的值来自当前进程的第三个参数(从 0 开始计数时的键 2)。让我们运行`node index.js Perlat` ( **Perlat** 是我的名字，所以你可以相应地修改它)

[![img](img/045d6c6b611643d257fc028bcd4d3243.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DrYoC4qm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/0%2AW6_aGP3AOUCJSZ17.png)

### 使其可执行

到目前为止，我们已经创建了我们的简单应用程序，我们可以通过运行`node index.js name`来运行它，但是我们需要在目录中才能工作。而且我们每次都需要运行节点，然后是文件，然后添加一个参数。

我们如何创建一个可执行文件来允许我们从任何目录运行命令呢？

答案是节点二进制！我们可以通过在`package.json`文件中添加一个新字段，即`bin`字段来轻松做到这一点。更多关于垃圾箱的信息请点击。现在，把它添加到你的 package.json 中，我通常把它添加在`main`字段之后。

```
"bin":{  "pawnhub":"./index.js"}, 
```

通过这样做，我们告诉节点我们想要注册一个名为`pawnhub`的命令，该命令将在运行时执行`./index.js`文件。我们可以通过指定名称和要执行的文件，在 bin 文件中添加多个可执行文件。

如果你不耐烦并且已经运行了命令`pawnhub name`，你将得到一个错误`command not found: pawnhub`这是因为命令没有被链接。我们可以通过在我们的目录中运行`npm link`来做到这一点，瞧！我们的软件包可以在我们的系统上全球符号链接！去试试吧。会失败的！

原因是我们需要在 index.js 文件的最顶端添加`#!/usr/bin/env node`。

通过添加它，我们告诉* nix systems JavaScript 文件的解释器应该是`/usr/bin/env node`，它查找本地安装的`node`可执行文件。

在 Windows 中，这一行将被忽略，因为它将被解释为注释，但是**必须在那里**，因为`npm`将在安装 NodeJS 命令行包时在 Windows 机器上读取它。现在再试一次，它应该工作正常！

[![img](img/06585d2b3f8b8b730fbd8c2f60c65e79.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sVqRq-eq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/0%2AdxDyOcaGh_GDmyxG.png)

现在我们有了一个接受参数的包，可以全局访问。我们需要开始工作，我们的最后一包，使 HTTP 请求 haveibeenpawned.com 网站。

### 安装 Axios

Axios 是什么？

Axios 是一个基于 promise 的 HTTP 客户端，用于浏览器和 node.js。我们可以发出 GET、POST、DELETE 或 PUT 等请求。我们将只使用 GET 请求。

更多关于 axios [的信息点击这里](https://github.com/axios/axios)。

因为 Axios 是一个 npm 包，我们可以通过运行`npm install axios`来安装它，它将被安装在我们的项目上。通过使用 CDN 或文件路径，Axios 也可以用于浏览器应用程序，例如:

```
<script src="https://unpkg.com/axios/dist/axios.min.js"></script> 
```

### 使用 Axios

这是一个使用 axios 的简单 GET 请求！(根据正式文件上的示例修改)

```
const axios = require('axios');// Make a request for a user with a given IDaxios.get('ENDPOINT')  .then(function (response) {    // handle the response when it is a success    console.log(response);  })  .catch(function (error) {    // handle when error ocurrs (eg: 404)    console.log(error);  }) 
```

### 使用 Haveibeenpawned.com API

去试试 https://haveibeenpwned.com 的网站(简称 HIBP ),看看你是否有一个被泄露数据的账户。我们将使用他们的 API 来实现我们想要的功能。

使用 API 的[文档](https://haveibeenpwned.com/API/v2)，供参考。

好，现在让我们请求 HIBP API。修改`index.js`以反映以下代码

```
#!/usr/bin/env node
const axios = require('axios');
axios.get(`https://haveibeenpwned.com/api/v2/breachedaccount/admin@test.com`)
.then(response=>{
     console.log(response.data)
})
.catch(err=>{
    console.log(err)
}) 
```

我们调用 API 在被攻破的账户中搜索 admin@test.com 的电子邮件。

现在运行`pawnhub`，你应该得到一个大的 JSON 对象，如下例所示。

[![img](img/dfd79ead965b9958cc6c84ab85ab14a3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--K47oVW0Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/0%2A9-WkuUb1tN8VklGw.png)

但是我们不需要来自那个 JSON 对象的所有信息…

让我们编辑该文件以反映以下内容:

```
#!/usr/bin/env node
const axios = require('axios');
axios.get(`https://haveibeenpwned.com/api/v2/breachedaccount/admin@test.com`)
.then(response=>{
let breaches=[]; //create empty array 
    console.log(`admin@test.com was found in ${ response.data.length} breaches!`) 
    //iterate each breaches to get only specific attributes
    response.data.forEach(breach => {
        breaches.push({
            Name:breach.Name,
            Domain:breach.Domain,
            BreachDate:breach.BreachDate,
            AccountsHacked:breach.PwnCount,
        })
    });
    console.table(breaches) //display in pretty table! 😀 
})
.catch(err=>{
    console.log(err)//display error
}) 
```

现在，我们应该有一个更好的数据表示，它应该类似于以下内容:

[![img](img/ba3533567adbc5a4d9e7ff87b4758721.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0C9CqDyi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/0%2AG60nLnYEZo5WVKLo.png)

很好，但是这不是动态的，我们已经硬编码了电子邮件…我们如何改变这一点？当然是通过使用参数！

让我们将代码修改如下:

```
#!/usr/bin/env node
const axios = require('axios');
const email = process.argv[2] || 'admin@test.com'; //get the email from the arguments or set a default value
axios.get(`https://haveibeenpwned.com/api/v2/breachedaccount/${email}`)
.then(response=>{
let breaches=[]; //create empty array 
    console.log(`${email} was found in ${ response.data.length} breaches!`) 
    //iterate each breaches to get only specific attributes
    response.data.forEach(breach => {
        breaches.push({
            Name:breach.Name,
            Domain:breach.Domain,
            BreachDate:breach.BreachDate,
            AccountsHacked:breach.PwnCount,
        })
    });
    console.table(breaches) //display in pretty table! 😀 
})
.catch(err=>{
    console.log(err)//display error
}) 
```

我们做到了！

我们现在可以通过运行`pawnhub THE_EMAIL@DOMAIN.TLD`向 API 查询我们想要的任何电子邮件，并检查该电子邮件是否已被破坏！那现在怎么办？好了，现在我们需要做最后一步，在 NPM 注册中心发布这个包！

### 发布包

### 认证

很明显，你需要创建一个账户来发布到 NPM 注册中心！

要在 NPM 注册中心创建账户，请点击[此处](https://www.npmjs.com/signup)。

创建帐户后，您需要通过运行命令`npm login`来验证我们的身份，系统会提示您提供我们的详细信息，您需要输入所需的详细信息并登录！

要测试登录是否成功，请输入命令`npm whoami`，您的用户名应该会显示在 CLI 中。

### 发布

现在是我们的软件包面向全球社区发布的最后一步！打开终端，在项目目录中运行下面的命令`npm publish --access public`,这将发布可以公开访问的包。并将在国家预防机制登记册上公布。对于这个例子，我选择的名字是`@perlatsp/pawnhub`，现在可以从[这里得到](https://www.npmjs.com/package/@perlatsp/pawnhub)。确保在 package.json 文件中更改名称！

[![img](img/568bcf577747012aec49d5ece8f4da65.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zFgSsp-Y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/0%2AaiLWgGiz9fylRByD.png)

嘣！💥任务完成！✅

我们刚刚学习了如何创建 NPM 包并将其发布到 NPM 注册中心。下一步是通过添加更多功能或修复任何错误来改进我们的包…🐞

### 测试包

好了，我们已经发布了我们的包..我们怎么能确定一切都按计划进行呢？

现在在你的终端上运行`npm unlink`来移除我们在[上创建的符号链接，这一步](https://kociaj.com/how-to-create-and-publish-npm-packages/#header-n610)通过运行`npm install -g YOURPACKAGENAME`来安装我们全新的包，我正在使用`npm install -g @perlatsp/pawnhub`。我们刚刚安装了我们的包，并通过`pawnhub`全球可用(记住这是我设置的包名，你的应该不同。通过运行`pawnhub any@email.com`进行测试，并检查结果。对于本教程，我们没有做任何错误处理，所以在电子邮件无效或不存在任何数据违规的情况下，您可能会得到未处理的错误。

现在我们刚刚完成了这个教程。

去做一些创建工具和包吧！🙆‍♂️

如果有任何问题，请告诉我！❓

如果你遵循了这个教程，请在你创造的令人敬畏的工具下面做评论！