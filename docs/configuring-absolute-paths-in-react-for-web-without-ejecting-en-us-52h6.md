# 在 React for Web 中配置绝对路径而不弹出[en-US]

> 原文：<https://dev.to/bybruno/configuring-absolute-paths-in-react-for-web-without-ejecting-en-us-52h6>

在你完成了一篇展示如何在 React Native 中设置[绝对路径的文章之后，现在我已经向你展示了如何在 React for Web 中这样做。](https://dev.to/bybruno/configuring-absolute-paths-in-react-native-en-us-2m5k)

虽然 React 团队没有在 CRA 实现这一点，但我们将自己动手设置，不需要弹出。继续阅读，你会看到奇迹发生。

## ☕小介绍

### 为什么不弹出项目？

嗯，原因是你打破了 CRA 的“保证”。但是冷静，我已经看到几个项目被驱逐，他们仍然在生产中完美地工作，唯一的问题是驱逐，设置将是我的，我必须支持他们。

“东西会碎”——[丹·阿布拉莫夫](https://twitter.com/dan_abramov/status/1045809734069170176)

但幸运的是，使用像 craco 这样的工具，如果出现问题，我们可以很容易地回到 CRA 的默认设置。这真是不可思议！

由于我们将只触及*别名*，所以您不必太担心，craco 将把我们在 *craco.config.js* 文件中所做的新设置注入到 CRA 默认设置中。

如果您不知道，在使用 Reactjs 创建的项目中配置绝对路径的目的是为了方便文件的导入。为此，我们可以用一个符号来表示代码的根目录。请看下面的例子:

```
 Use this 😍
import Form from '@/components/Form'

E Avoid this 😤
import Form from '../../../../../components/Form' 
```

Enter fullscreen mode Exit fullscreen mode

## 他们说学习是一种实践🏊

☝所以来吧，打开你的终端，安装必要的依赖:

```
# yarn
yarn add @craco/craco

# npm
npm i @craco/craco 
```

Enter fullscreen mode Exit fullscreen mode

✌在执行了 *craco* 安装之后，我们将需要重命名一些 *package.json* 命令行。

将“反应脚本”替换为*“craco”*。

```
{  "scripts":  {  "start":  "craco start",  "build":  "craco build",  "test":  "craco test",  } 
```

Enter fullscreen mode Exit fullscreen mode

这将导致 CRA 脚本由 *craco* 执行，它将注入 *craco.config.js* 文件中的设置。

🛠在项目根目录下创建名为 *craco.config.js* 的文件，并添加以下设置:

```
const path = require('path');

module.exports = {
  webpack: {
    alias: {
      '@': path.resolve(__dirname, 'src/')
    }
  },
  jest: {
    configure: {
      moduleNameMapper: {
        '^@(.*)$': '<rootDir>/src$1'
      }
    }
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

## 一撮 VueJS，请！🍲

我使用的是 Vuejs 使用的*别名*“@”复制。你可以用你觉得有趣的*别名*，比如“~”或者“#”。

## 我的虚拟代码什么都不懂😢

在这样做的时候，我们会遇到第一个问题，自动完成。当我们使用相对路径导入文件时，我们已经习惯了自动完成。

发生此错误是因为 VSCode 不理解“@”是我们项目的“src”文件夹。要启用自动完成功能，我们需要配置 VSCode，以便它能够理解。为此，我们需要在项目根目录下创建一个名为 *jsconfig.json* 的文件。

了解更多关于 [jsconfig.json](https://code.visualstudio.com/docs/languages/jsconfig) 的信息。

在文件中包含以下属性:

```
{  "compilerOptions":  {  "baseUrl":  "src",  "paths":  {  "@/*":  ["./*"]  }  },  "exclude":  ["node_modules",  "**/node_modules/*"]  } 
```

Enter fullscreen mode Exit fullscreen mode

太神奇了！

[![](img/ee00943ab1ba036652244152faed86c4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0kRe_1US--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d8dnvamm9u4v3xhp0hl3.png)

现在它可以完美地工作了，使用命令:npm start 来运行您的项目。

## 一个喜欢一直抱怨的工具！😡

如果您使用的是 *eslint* ，您会注意到它会抱怨您使用“@”执行的所有导入。

感谢上帝，有一种方法可以使用 [eslint-import-alias](https://www.npmjs.com/package/eslint-import-resolver-alias) 来平息 *eslint* 。

看在上帝的份上，☝首先将下面的库添加为开发依赖项！😰

```
# yarn
yarn add eslint-plugin-import eslint-import-resolver-alias -D

# npm
npm i eslint-plugin-import eslint-import-resolver-alias -D 
```

Enter fullscreen mode Exit fullscreen mode

你的. eslintrc.json 文件中的✌包括以下属性:

```
 "settings": {
    "import/resolver": {
      "alias": {
        "map": [["@", "./src"]]
      }
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

## 看似愚蠢实则不然的问题😳💬

#### 我可以在投入生产的项目中使用这种技术吗？

回答:可以，用起来没有任何问题！

#### 可以在 React Native 中使用吗？

回答:不会，React Native 中的设置不一样，我给你演示一下这篇文章怎么做:
[在 React Native 中配置绝对路径](https://dev.to/bybruno/configuring-absolute-paths-in-react-native-en-us-2m5k)。

#### 我的测试文件会失败吗？

回答:如果你一步一步地正确遵循，它可能不会给你一个错误。如果您已经更改了将要用作*别名*的符号，请确保您也将它放在了 craco.config.js 文件的 jest 配置中的 *moduleNameMapper* 属性中。

#### 你为什么不用巴别塔插件 root 导入？

回答:与 Babel 插件根导入不同，我们只导入一个解决问题的库，并且简单易用。另一个一直困扰我的是，它不支持 CRA 3.0，所以使用 Craco。

## 想象一下，如果生活中的一切都完美运转🦄

像任何库一样，您可能会在@craco 中发现错误，如果发现，请在官方项目中为社区打开一个[问题，以改进库并使其对每个人都可用。](https://github.com/sharegate/craco/issues)

但是很酷，为了这个教程无所畏惧地使用它。

## 该说再见了😩

我真的很享受和你在一起的时光。如果你想知道我在做什么，请在 Twitter 上关注我。

通过分享这篇文章，帮助您的开发人员朋友停止遭受相对进口的痛苦！

一个大大的拥抱，下次再见！