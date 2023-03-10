# 盖茨比 JS 源码插件教程一步一步给初学者

> 原文：<https://dev.to/robmarshall/gatsby-js-source-plugin-tutorial-step-by-step-for-beginners-4815>

本文旨在帮助开发人员开始创建 Gatsby JS 源代码插件。

我花了一段时间来理解创建插件背后的方法和原因，所以希望我可以节省你一些时间！我正在构建我的[gatsby-source-gravity forms](https://github.com/robmarshall/gatsby-source-gravityforms)插件，这时我发现 Gatsby 插件文档——尽管很棒——缺少我学习时需要的细微差别和解释。

我喜欢 Gatsby JS 的地方在于它大大简化和加快了开发过程。这种速度部分归因于源代码插件获取原始数据并将其转化为可用的 GraphQL 数据。

_ 如果你想了解更多关于 GraphQL 的信息，[请访问网站](https://graphql.org/)。_

## 什么是源码插件？

[![Sauce meme based off Finding Nemo film](img/675b6c5a0c879b5e37e253ac6aa618b3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--W06APA-u--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.kym-cdn.com/photimg/newsfeed/000/705/119/926.jpg)

源代码插件是一个单一用途的工具，用于将特定的第三方系统与 Gatsby 集成。单一目的非常重要，因为一个插件应该只与一个第三方系统集成。这使得容易混合和匹配，而没有过多的混乱。

一个很好的例子是 [Gatsby 源代码 WordPress](https://www.gatsbyjs.org/packages/gatsby-source-wordpress/) 插件。它与 WordPress Rest API 连接，并将数据拉入 Gatsby，然后转换为 GraphQL 节点。

如果您想充分发挥 Gatsby 的潜力，为特定 API 创建定制源代码插件的能力是一个巨大的优势。如果您依赖于许多微服务，而不是只使用一个后端，情况尤其如此。我的关于内容网的兴起的文章对此进行了更详细的探讨。

创建自己的插件不仅在使用定制数据时有意义，而且由于生态系统如此年轻，你可能**有**来创建自己的插件。有可能你需要的东西并不存在！

## 我们在制造什么？

盖茨比生态系统缺少了一个重要的东西。一个引入猫图像的插件。必不可少！

[![](img/ff7e0db9b15604869713e56e914063f2.png)](http://rest.thoughtsandstuff.com/wp-content/uploads/2019/11/New-Project-16-2.jpg)

作为一组基本要求，我认为这个插件必须:

*   连接到 thecatapi.com 的 API
*   从 API 获取预定数量的猫图像
*   将它们添加到 GraphQL 中，以便在 Gatsby 站点中使用

想看成品的话，看一下这个回购:[https://github.com/robmarshall/gatsby-source-randomcat](https://github.com/robmarshall/gatsby-source-randomcat)

## 安装

### 盖茨比 CLI

在本教程中，我们将使用 [Gatsby CLI](https://www.gatsbyjs.org/docs/gatsby-cli/) 来设置和运行 Gatsby。使用以下命令安装它:

```
npm install -g gatsby-cli 
```

Enter fullscreen mode Exit fullscreen mode

### 开发项目

您还需要一个项目来运行开发。为此，我们将使用 Gatsby Starter 项目。它包括一个基本的设置，非常适合测试！

`gatsby new my-project-name https://github.com/gatsbyjs/gatsby-starter-default`

现在进入文件夹并安装所有依赖项。从现在起我们将使用纱线。

`cd my-project-name
yarn`

现在这是完全回到你的根。

```
cd ../ 
```

Enter fullscreen mode Exit fullscreen mode

### 启动器源码插件

下一步是设置源代码插件。为了帮助你开始，我创建了一个基本的库，包含了启动一个源代码插件所需的基本文件:[https://github.com/robmarshall/gatsby-source-starter](https://github.com/robmarshall/gatsby-source-starter)。

这应该被克隆到 tester 项目之外它自己的项目文件夹中。这意味着您可以单独处理它，然后将它包含在您的其他本地项目中。

```
git clone https://github.com/robmarshall/gatsby-source-starter.git my-plugin-name 
```

Enter fullscreen mode Exit fullscreen mode

### 文件夹布局

现在你应该有一个类似这样的文件夹布局:

*   项目父文件夹
    *   Gatsby 起始文件夹
    *   源插件文件夹

现在一切都安装好了，最后要做的是将插件链接到项目文件夹。这只是发展需要。一旦插件推出，它可以作为一个正常的 NPM 软件包安装。

## 链接你的插件进行开发

我们不是在当前项目中构建插件，而是单独构建它。这使得它可以用于多个本地项目，而不需要复制。这可以通过使用 **NPM 连杆**或**纱线连杆**来实现。

这两个命令的工作方式略有不同(我现在不会深入讨论)，我一直认为 **Yarn link** 更可靠。

在链接你的插件之前，确保你的源插件的 package.json 文件的名字已经更新为你想要的名字。

### 纱链

要将您的插件文件夹连接到您的测试文件夹，您需要执行以下操作:

1.  导航到您的插件目录

```
cd gatsby-source-starter 
```

Enter fullscreen mode Exit fullscreen mode

1.  运行 Yarn link 命令创建一个全局包

```
yarn link 
```

Enter fullscreen mode Exit fullscreen mode

1.  导航到您的 Gatsby tester 站点文件夹

```
cd gatsby-source-starter 
```

Enter fullscreen mode Exit fullscreen mode

1.  运行 Yarn link 来创建一个指向你刚刚创建的插件文件夹的符号链接。这将允许你移动或重命名插件文件夹，链接会自动更新。

```
yarn link "gatsby-source-starter" 
```

Enter fullscreen mode Exit fullscreen mode

这个过程可以通过
逆转

```
cd /gatsby-source-starter
yarn unlink "gatsby-source-starter"

# Then remove the package:
cd /gatsby-source-starter
yarn unlink 
```

Enter fullscreen mode Exit fullscreen mode

现在你的插件已经连接到你的测试站点了。对插件的任何更改都会自动反映在项目中。

## 让我们做点什么吧！

[![Jonah Hill so excited](img/a02ebf1b8202521dd5cd60be5f6a7b12.png)](http://rest.thoughtsandstuff.com/wp-content/uploads/2019/11/tenor.gif)

首先，进入插件文件夹。

```
cd gatsby-source-starter 
```

Enter fullscreen mode Exit fullscreen mode

一旦进入该文件夹，您将需要安装一些有用的软件包。

### 安装包

当插件从 REST API 获取数据时，你需要使用[获取](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)、 [Axios](https://github.com/axios/axios) 或者类似的技术。为了方便起见，我选择了使用 Axios。

最重要的是，我喜欢看到我的插件运行时发生了什么。为了让控制台日志更漂亮，我还加入了粉笔。

安装这两个软件包:

### `yarn add axios chalk`

### 盖茨比-node.js

这就是奇迹发生的地方。该文件中的代码在构建站点的过程中运行。它用于在 GraphQL 中添加节点，以及在构建生命周期中响应事件。

在编辑器中打开该文件，您应该会看到如下内容:

```
let activeEnv =
    process.env.GATSBY_ACTIVE_ENV || process.env.NODE_ENV || 'development'

if (activeEnv == 'development') {
    process.env.NODE_TLS_REJECT_UNAUTHORIZED = '0'
}

exports.sourceNodes = async (
    { actions: { createNode }, createContentDigest, createNodeId },
    { plugins }
) => {
    // Do your stuff!
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们在开发环境中使用一个假的 SSL 证书，上面的 6 行代码确保节点运行时没有任何错误。

最下面的 6 行介绍了 sourceNodes 函数，您将需要使用它向 GraphQL 添加数据。

sourceNodes 函数公开了稍后需要的几个函数。其中包括:

*   创建节点
*   createContentDigest
*   createNodeId

关于这些的深入解释，请看源代码插件启动概述。

首先，将这几行添加到页面顶部。

```
const axios = require("axios");
const chalk = require("chalk");
const log = console.log; 
```

Enter fullscreen mode Exit fullscreen mode

这将导入所需的包，并简化整个插件中 console.log 的使用。

接下来，在 sourceNodes 函数中添加以下代码，其中有一行“// Do your stuff”。

```
log(chalk.black.bgWhite("Starting Random Cat Source plugin"));
if (!api) {
  log(
    chalk.bgRed("You seem to be missing API details in your gatsby-config.js")
  );
  return;
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 得到猫

既然已经设置好了，我们就可以与 cat API 交互了。这由下面的代码处理。

```
let result;

try {
  result = await axios.get(
    `https://api.thecatapi.com/img/search?limit=${limit ? limit : "5"}`,
    {
      responseType: "json",
      headers: { "x-api-key": api }
    }
  );
} catch (err) {
  log(chalk.bgRed("There was an error"));
  log(err);
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们将数据作为“结果”变量中的一个对象。最后一步是遍历每个 cat 对象，并将其作为节点添加到 GraphQL。为此，我们将使用 sourceNode 函数带来的三个函数。

```
if (result.data) {
  for (const [key, value] of Object.entries(result.data)) {
    let catObj = result.data[key];
    let newCatNode = {
      id: createNodeId(`random-cat-${catObj.id.toString()}`),
      internal: {
        type: "Random__Cat",
        contentDigest: createContentDigest(catObj)
      },
      catId: parseInt(catObj.id),
      url: catObj.url,
      width: catObj.width,
      height: catObj.height,
      breeds: catObj.breeds
    };
    createNode(newCatNode);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这段代码检查我们是否有要循环的数据。如果我们这样做了，那么我们就遍历包含猫的对象的关键点。newCatNode 用于打包每个 cat，并传递给 createNode 函数。

要创建节点，对象必须包括:

*   id(使用 createNodeId 函数)
*   内部的
    *   类型(唯一的节点类型名称)
    *   内容摘要(使用 createContentDigest 函数)
*   要添加到节点的任何数据

该 ID 必须完全唯一。因此，我使用插件名，加上特定 API 节点的 ID。这确保了一个完全唯一的密钥。

contentDigest 的目的是在数据没有改变的情况下阻止 Gatsby 重新创建节点。它接受所有要创建的节点并对其进行哈希处理。如果这个数据改变了，散列也改变了，盖茨比知道该更新了。

## 这就对了

现在这只是一个非常小的插件，你可能要构建更大的插件。然而，过程仍然是相同的。所有东西都需要通过 createNode 函数传递，使用唯一的类型名和 id。

最后一个插件:[https://github.com/robmarshall/gatsby-source-randomcat](https://github.com/robmarshall/gatsby-source-randomcat)

举个例子:https://github.com/robmarshall/gatsby-randomcat-frontend

在 Twitter 上让我知道你对这篇文章的想法，以及它是否需要扩展。如果你想更深入地了解某个部分，请告诉我，我会把它添加进去！