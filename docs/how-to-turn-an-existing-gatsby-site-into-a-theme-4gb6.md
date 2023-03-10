# 如何将现有的盖茨比网站变成一个主题

> 原文：<https://dev.to/laurieontech/how-to-turn-an-existing-gatsby-site-into-a-theme-4gb6>

如果你没有看到我们做这个的现场直播，请点击这里查看！虽然这篇文章信息丰富，将涵盖我们工作的大部分内容，但我真的无法再现那种程度的喜剧和戏谑。 [@jlengstorf](https://dev.to/jlengstorf) 就是一个爆。

[https://www.youtube.com/embed/ciGFxNamooI](https://www.youtube.com/embed/ciGFxNamooI)

此外，完全归功于 [@likeomgitsfeday](https://dev.to/likeomgitsfeday) ，他的网站是我的原始灵感(和架构)！

以此为基础，让我们深入研究代码。

### 项目设置

我的项目存在于一个有点杂乱的个人目录中。首先，我们要将它移到一个新文件夹中。`site-and-stuff`这个难以置信的名字。这不是绝对必要的，它只是有助于组织的目的。

从那里，是时候设置主题项目和相关的纱线工作空间了。

### 初始化主题

在`site-and-stuff`中，我将创建一个名为`gatsby-theme-speaking-yaml`的目录。这意味着`site-and-stuff`当前包含那个主题项目和我最初的站点项目`gatsby-laurieontech`。

现在我们要初始化我的主题项目。为此，我们将在`gatsby-theme-speaking-yaml`中运行以下命令。

```
yarn init -y 
```

Enter fullscreen mode Exit fullscreen mode

这将创建一个包含一些现有信息的 package.json。但是，我们还想添加一些额外的项目，`author`和`keywords`。整个事情最终看起来像这样。

```
{  "name":  "gatsby-theme-speaking-yaml",  "version":  "1.0.0",  "main":  "index.js",  "license":  "MIT",  "author":  "Laurie on tech",  "keywords":  ["gatsby",  "gatsby-plugin",  "gatsby-theme"]  } 
```

Enter fullscreen mode Exit fullscreen mode

注意我们的`main`指向`index.js`。该文件当前不存在，因此我们需要创建它。目前它不需要任何内容，所以我们现在只需要添加一个评论就足够了。

### 设置工作区

我们还需要在根目录`site-and-stuff`中创建一个 package.json。在这种情况下，我们只需创建文件并手动添加一些信息。最终看起来像这样。

```
{  "private":  true,  "workspaces":  ["gatsby-laurieontech",  "gatsby-theme-speaking-yaml"]  } 
```

Enter fullscreen mode Exit fullscreen mode

要测试这是否可行，您可以运行以下命令。

```
yarn workspaces info 
```

Enter fullscreen mode Exit fullscreen mode

### 将主题安装到站点中

接下来，我们将我们的主题添加到我们的网站。但是我们必须先做一件快速的事情。

我的站点的部分引用数据位于`gatsby-laurieontech`项目的 package.json 中的键值对中。我可能忘了也可能没忘了更改默认设置，但现在是`laurieontech`。为了可读性，该代码片段被截断。

```
{  "name":  "laurieontech",  "description":  "Laurie never changed the starter description, oops",  "version":  "1.0.0",  "author":  "Laurie Barth"  } 
```

Enter fullscreen mode Exit fullscreen mode

这很重要，因为这是我们在 yarn 工作空间中引用站点项目的方式。所以现在我们可以将主题添加到站点中。

```
yarn workspace laurieontech add gatsby-theme-speaking-yaml@* 
```

Enter fullscreen mode Exit fullscreen mode

是这样写的，用的是`@*`，因为主题未发表。

要测试这是否可行，您可以再次运行该命令。您应该会看到站点信息中列出的主题。

```
yarn workspaces info 
```

Enter fullscreen mode Exit fullscreen mode

我们还想将主题作为插件添加到我们的站点`gatsby-laurieontech`的 gatsby-config.js 文件中。

```
module.exports = {
  plugins: [
    'gatsby-theme-speaking-yaml',
    // other stuff goes here
  ],
} 
```

Enter fullscreen mode Exit fullscreen mode

### 将依赖项安装到主题中

现在我们想开始开发主题本身。要做的第一件事是找出主题有哪些依赖关系。在我们的例子中，我们查询 yaml 并处理图像。所以我们将安装这些依赖项。

```
yarn workspace gatsby-theme-speaking-yaml add gatsby-source-filesystem gatsby-image gatsby-plugin-sharp gatsby-transformer-yaml gatsby-transformer-sharp 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们想在我们的主题中创建一个`gatsby-config.js`文件。我们将在这里添加刚刚安装的插件。

```
module.exports = {
  plugins: [
    `gatsby-transformer-yaml`,
    `gatsby-transformer-sharp`,
    `gatsby-plugin-sharp`,
    {
      resolve: `gatsby-source-filesystem`,
      options: {
        path: `${__dirname}/src/data/`,
      },
    },
  ],
} 
```

Enter fullscreen mode Exit fullscreen mode

> 注意，gatsby-image 需要安装，但不包括在内，它是一个实用程序

### 创建主题

现在是时候实际创建我们的主题了！由于我们是在现有站点的基础上构建我们的主题，所以我们可以从我的站点中取出我们想要的任何代码，并将其放入主题项目中。

在本例中，这是我的 speaking.js 页面中的一个部分。所以我们将创建`src/component/speaking.js`并将相关的`JSX`移过来。

现有的代码使用页面查询，主题将这个部分视为一个组件，因此它需要使用静态查询。我们会像这样改变。

```
import React from 'react'
import { graphql, useStaticQuery } from 'gatsby'
import Img from 'gatsby-image'
import Event from './event'

const SpeakingSection = () => {
  const data = useStaticQuery(graphql`
    {
      allSpeakingYaml(sort: { fields: [index], order: DESC }) {
        edges {
          node {
            conference
            year
            url
            date
            location
            image {
              childImageSharp {
                fluid {
                  ...GatsbyImageSharpFluid
                }
              }
            }
            talks {
              title
              video
              type
            }
          }
        }
      }
    }
  `)
  return (
    <section id="speaking" className="wrapper style4 container">
      <div className="container">
        <h2>Appearances</h2>
        <div className="wrapper" id="speakwrap">
          {data.allSpeakingYaml.edges.map(({ node }) => (
            <Event event={node} />
          ))}
        </div>
      </div>
    </section>
  )
}

export default SpeakingSection 
```

Enter fullscreen mode Exit fullscreen mode

### 确保数据可用

现在从我的主题中提取的代码使用了我的原始项目中的数据，而不是主题中的数据。所以我认为需要移到，但实际上没有！

> 这就是 Laurie 意识到主题不需要保存数据的地方，当所有东西都被编译在一起时，它将能够看到我所有的 yaml 内容！

然而，为了确保它的工作，我们需要检查主题的`gatsby-config.js`,以确保它使用了正确的相对路径。在我的例子中，这意味着对`gatsby-source-filesystem`插件配置的微小改变。

```
module.exports = {
  plugins: [
    {
      resolve: `gatsby-source-filesystem`,
      options: {
        path: `src/data/`,
      },
    },
  ],
} 
```

Enter fullscreen mode Exit fullscreen mode

### 导出主题组件

我们主题的核心存在于`src/components/speaking.js`中，但是我们的`package.json`引用了一个只有注释的`index.js`文件。因此，我们需要确保该文件正在导出我们的主题内容。

```
export { default as SpeakingEvents } from './src/components/speaking' 
```

Enter fullscreen mode Exit fullscreen mode

事实证明，我们可以同时进口和出口！我真的应该把这个技巧加到这个帖子里。

[![laurieontech image](img/ca0667c84fcd9741a29fbc42fbc0816c.png)](/laurieontech) [## 出口、进口和违约，天啊！

### 劳丽 Apr 29 ' 19 分钟阅读

#javascript #react #webdev #beginners](/laurieontech/exports-and-imports-and-defaults-oh-my-fa3)

### 使用主题！

现在我们可以使用我们的主题，就好像它只是我们 Gatsby 站点中的另一个组件一样。

在这种情况下，这意味着更改我的站点的发言页面，并从我的主题中导入组件来替换现有的代码。注意，导入是绝对的，因为它相当于从另一个项目中引入一个导出！

```
import React from 'react'
import Layout from '../components/layout'
import Hero from '../components/Hero'
import { SpeakingEvents } from 'gatsby-theme-speaking-yaml'

const SpeakingPage = ({ data }) => {
  return (
    <Layout>
      <Hero /> // this is a local component! I can use all this interchangeably
      <SpeakingEvents />
    </Layout>
  )
}

export default SpeakingPage 
```

Enter fullscreen mode Exit fullscreen mode

### 用主题经营你的网站！

现在是时候用我们的主题运行我们的项目了。

记住，我们实际上使用的是在`package.json`中指定的`name`，而不是站点的目录名，就像这样。

```
yarn workspace laurieontech develop 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！现在我们可以添加主题，发布它，无论我们想要什么。

### 我们讨论过的其他东西

这是一条相当活跃的河流，所以自然会出现其他的东西。重要主题，例如:

*   杰森相对于盖茨比其他员工的身高

*   我对时区的恐惧

*   末端的...和命令，劳里几乎用它们把她的电脑变成了砖头

哦，你是说和我们的实际目标有关？

我们对我的应用程序做了一些重构，讨论了盖茨比形象和其他一些事情。

在某个时候，我会写一篇关于我的网站完全重构的文章。

但在那之前，我迫不及待地想看到你们都把自己在个人网站上所做的工作作为一个主题供他人利用。

我们站在摆在我们面前的工作的肩上！

> ![Laurie profile image](img/ed82805e4048b1b025fcda51dccd1038.png)劳里[@ laurieontech](https://dev.to/laurieontech)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)嘿👋🏻我是劳里，我站在别人的肩膀上。我复制和粘贴代码，寻找有片段的博客文章，有用的 git 库，等等。
> 没事！它帮助我走得更快。通过查看工作示例和迭代，我学得更好。我们中没有一个人能独自做到这一点。2019 年 7 月 30 日下午 15:53[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1156231444945092609)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1156231444945092609)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1156231444945092609)