# 什么是单库，为什么你应该尝试

> 原文：<https://dev.to/anonimoconiglio/what-is-a-mono-repository-and-why-you-should-try-lerna-57lm>

### 了解如何使用 Lerna 建立一个 Gatsby 主题项目:本指南将帮助你理解什么是 mono-repo，以及如何使用 NPM 而不是 Yarn 工作空间来管理它。

## 索引

*   什么是 Lerna，为什么值得一试？
*   [将蕾娜与 NPM 从贝壳中分离出来](#setting-lerna-with-npm-from-the-shell)
*   [用外壳搭建文件夹和文件](#scaffolding-folders-and-files-with-shell)
*   [使用 Lerna](#using-lerna)
    *   [选择你的方法](#choose-your-approach)
*   [用 Lerna bootstrap 协调一切](#orchestrate-everything-with-lerna-bootstrap)
*   [运行命令和脚本](#run-commands-and-scripts)
*   [更新向导](#update-wizard)

## 什么是 Lerna，为什么值得一试？

我开始跟随杰森·伦斯托夫( [@jlengstorf](https://dev.to/jlengstorf) )的[盖茨比主题创作课程](https://egghead.io/courses/gatsby-theme-authoring)，很快意识到我对‘纱线工作空间’一无所知。这是管理所谓的单一回购的一种很好的方式:一个内部有不止一个项目并可以共享依赖关系的存储库。

由[双 mutzi . github . I](https://doppelmutzi.github.io/monorepo-lerna-yarn-workspaces/)发出的双 mutzi:

> 这样的项目被称为工作区或包。相反，使用多个存储库，每个存储库只包含一个项目，这被称为多回购方法。[...]

单一回购方法让我惊讶的一点是，我的*包/工作区*可以本地共享相同的`node_modules`依赖项。这为我节省了时间和物理空间。

> lerna 和 yarn 工作区让我们能够在单个 repo 中构建库和应用程序，而无需强迫我们发布到 npm 或其他注册表。这些技术背后的美妙之处在于，它们可以通过分析位于每个项目的根文件夹中的 package.json 文件来找到包的依赖关系。因此，这些工具使得手动创建符号链接或直接使用“低级”npm 链接变得过时。

现在，即使你已经在使用`yarn workspaces`了，我认为 Lerna 值得一试，因为它提供了一系列的优势。此外，您可以将 Lerna 与 Yarn Workspaces 一起使用:

> 使用一个 lerna 命令，您可以遍历所有或特定的包，对每个包运行一系列操作(比如林挺、测试和构建)。因此，它补充了接管依赖性管理过程的 yarn 工作空间。

所以最后我决定学 Lerna——抱歉绕口令——但是我不想转到 Yarn(因为我只是对 npm 很舒服)。以下是我如何成功做到的:

## 设定莱娜与 NPM 脱离外壳

为您的项目创建一个目录，并使用以下命令进入该目录:

```
mkdir try-lerna
cd try-lerna 
```

可以全局安装 Lerna】)或者和 npx 一起使用。

*   启动粘土

    ```
    npx lerna init --independent 
    ```

这将:

*   一个 git 存储库
*   创建一个`package.json`
*   创建一个`lerna.json`
*   创建一个`packages`文件夹(您可以在这里放置您的工作区)。

> lerna 称*工作区*为**包**。

你可以随意排列*包*，如果你愿意，你可以把所有的包放在根文件夹中，然后改变排列以匹配(就像“yarn workspaces”方法)。

我的 **lerna.json** 文件有`stream: true`(为了用 lerna 运行 develop 时看到 gatsby 输出)和`hoist: true`(为了*提升* node_modules 也用命令`lerna add`；我将向前解释这最后一个特征):

```
{  "packages":["packages/*"],  "version":"independent",  "stream":true,  "command":{  "bootstrap":{  "hoist":true  }  }  } 
```

您可以手动添加或者使用这个简单的 shell 命令:

```
echo "{\r\t\"packages\":[\"packages/*\"],\r\t\"version\":\"independent\",\r\t\"stream\":true,\r\t\"command\":{\r\t\t\"bootstrap\":{\r\t\t\t\"hoist\":true\r\t\t}\r\t}\r}" > lerna.json 
```

## 用外壳搭建文件夹和文件

这一部分可以在 Jason Lengstorf 课程的第一课之后手工完成。我只是收集了所有的基本段落，以便用 shell 的几个命令来完成它们。

如果你想使用我的方法，记得用你的主题名称替换“名称”，然后复制并粘贴这个命令:

```
mkdir -p packages/{gatsby-theme-NAME,site} 
```

```
touch packages/{gatsby-theme-NAME,site}/{index.js,package.json} 
```

```
echo "//loop" >> packages/{gatsby-theme-NAME,site}/index.js 
```

站点文件夹中 package.json 的初始内容:

```
echo "{\r\t\"private\":true,\r\t\"name\":\"site\",\r\t\"version\":\"1.0.0\",\r\t\"license\":\"MIT\",\r\t\"scripts\":{\r\t\t\"builds\":\"gatsby build\",\r\t\t\"develop\":\"gatsby develop\",\r\t\t\"clean\":\"gaysby clean\"\r\t}\r}" >> packages/site/package.json 
```

文件夹主题上的 package.json(再次替换名称，同时插入您的 _ NPM _ 用户):

```
echo "{\r\t\"name\": \"@YOUR_NPM_USER/gatsby-theme-NAME\",\r\t\"version\": \"1.0.1\",\r\t\"license\": \"MIT\",\r\t\"main\": \"index.js\",\r\t\"scripts\": {\r\t\t\"builds\": \"gatsby build\",\r\t\t\"develop\": \"gatsby develop\",\r\t\t\"clean\": \"gaysby clean\"\r\t}\r}" >> packages/gatsby-theme-NAME/package.json 
```

结果如下:

```
├── packages
│  ├── site
│  └── gatsby-theme-NAME
├── .gitignore
├── package.json
└── lerna.json 
```

PS:我建议你创建一个`.gitignore`然后马上添加`node_modules`:

```
echo "node_modules" > .gitignore 
```

## 使用 Lerna

Lerna 的一个问题是，你不能用一个命令为每个包安装多个依赖项(就像你用`yarn workspace name_workspace add package1 package2 etc`做的那样)。你需要一个接一个地做，就像这样(如果你没有全局安装它，你必须在每个命令前加上`npx`:

```
lerna add gatsby --scope site 
```

```
lerna add react --scope site 
```

```
lerna add react-dom --scope site 
```

这里`--scope site`用来告诉 Lerna 只安装在包“site”里。

然而，您可以只使用一个命令为每个*包*安装一个依赖项，比如说`gatsby`:

```
lerna add gatsby 
```

该命令将:

*   为每个包安装 Gatsby(在这种情况下，站点和主题包也是如此)。
*   如果您在 lerna.json 上启用了`hoist`，它将在您的根目录下创建一个 node_modules 文件夹(参见“lerna 引导向前”):

#### 选择你的方法

如果您喜欢这种方法，您可以继续在软件包上安装所有的公共依赖项(`react`，`react-dom`)。如果您只需要为一个包安装一些依赖项，请使用`--scope name_of_the_package`(记住它是 package.json 上指定的名称，而不是文件夹的名称)。

您也可以通过在相应的包中安装您想要的每个依赖项来使用 npm。但是请记住，这种方法将为每个文件夹安装一个 node_modules 依赖项。这可以是干净的，然后再吊起来。虽然这个选项是有效的，但它会花费你更多的时间。

无论您选择哪个选项，请记住添加/链接本地主题包作为站点包的依赖项(使用您在`package.json`上设置的名称):

```
lerna add @YOUR_NPM_USER/gatsby-theme-NAME --scope=site 
```

## 用 Lerna bootstrap 编排一切

最后你可以放心地做:

```
lerna bootstrap 
```

这个命令非常有用，特别是如果您已经用各自的依赖项填充了 package.json。

使用`hoist: true`选项(在`lerna.json`上)，它会将一个共享的`node_modules`文件夹放在根目录下，然后它会将本地依赖项链接到各自的`package.json`中。

我真的很喜欢 [-提升特性](https://github.com/lerna/lerna/tree/master/commands/bootstrap#--hoist-glob)这是一个避免重复依赖 node_modules 的过程，因此它节省了物理空间和安装时间。

无论出于什么原因，如果你想先清洗任何东西，你可以这样做:

```
lerna clean && lerna link && lerna bootstrap 
```

## 运行命令和脚本

要运行脚本(放置在`package.json`上),您需要指定(或不指定)目标包:

```
lerna run develop --scope Package_name 
```

Lerna 还允许你从根到每个包运行任何命令(使用 dash)。例如:

```
lerna exec -- rm -rf node_modules 
```

这将删除每个程序包文件夹中的节点模块。

## 更新向导

将来，当你需要更新你的软件包时，你可以使用[更新向导](https://www.npmjs.com/package/lerna-update-wizard):

```
npm install --save-dev lerna-update-wizard 
```

然后，您只需要运行这个命令，向导将帮助您:

```
lernaupdate 
```