# 在 React Native 中配置绝对路径[en-US]

> 原文：<https://dev.to/bybruno/configuring-absolute-paths-in-react-native-en-us-2m5k>

我想念 React / React Native 的一点是绝对路径。使用相对路径导入文件是非常常见的。

问题是当项目增长，文件夹嵌套很深的时候，我相信你可能已经看到或者已经这么做了:

```
../../../.../../../../Utils/Breadcrumbs.js

../../../../../Components/Form/TextField.js 
```

Enter fullscreen mode Exit fullscreen mode

现在假设 Utils 文件夹已经更改了目录。😢

为了解决这个问题，使用一个名为 *Babel Plugin Root Import* 的库。有一个列表，可以用来编码我们的应用程序的根，这是一个“src”文件夹。😍

## 学的是实践

☝将库添加到您的项目中。

```
 babylu@Project: ~$  yarn add babel-plugin-root-import -D
 ou babylu@Project: ~$  npm install babel-plugin-root-import -D 
```

Enter fullscreen mode Exit fullscreen mode

✌安装后，设置了位于根目录下的 **babel.config.js** 文件。

```
module.exports = {
  presets: ['module:metro-react-native-babel-preset'],
  plugins: [
    [
      'babel-plugin-root-import',
      {
        rootPathPrefix: '@',
        rootPathSuffix: 'src'
      }
    ]
  ],
  env: {
    production: {
      plugins: [
        'babel-plugin-root-import',
        {
          rootPathPrefix: '@',
          rootPathSuffix: 'src'
        }
      ]
    }
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

完成所有设置后，现在可以使用“@”作为前缀来导入文件。下面是一个例子:

**@/组件/表单**

**@/Pages/Auth/SignIn**

## 一撮 *VueJS* 请🍲

我正在使用“@”复制 *Vuejs* 。使用你感兴趣的前缀。例如，它可以是“~”或“#”。

## 劳驾，你能给我指一下路吗？🚶

使用这种技术，我们将有我们的第一个问题，缺乏自动完成。这是因为 VSCode 仍然不理解“@”指的是我们项目的“src”文件夹。为了解决这个问题，我们将在根目录下创建一个 ***VSCode*** 理解的配置文件，名为 *jsconfig.json* 。

其中包括以下设置:

```
{  "compilerOptions":  {  "target":  "es6",  "baseUrl":  ".",  "paths":  {  "@/*":  ["src/*"]  }  },  "exclude":  ["node_modules"]  } 
```

Enter fullscreen mode Exit fullscreen mode

了解更多关于 *jsconfig.json* 文件:【https://code.visualstudio.com/docs/languages/jsconfig】
T3

现在是了！😎

[![](img/3b7a6dc7d94e5f73d4846160232b61d1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--S4EOeu3E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1rbf5ujyinvkv5rirjv2.png)

## 一个喜欢抱怨的工具！😡

如果你在你的项目中使用了 *eslint* ，你将会收到很多关于你使用前缀“@”导入的投诉。

幸运的是，有一个库可以让你告诉 *eslint* 一切正常。

添加:

```
 babylu@Project: ~$  yarn add eslint-import-resolver-babel-plugin-root-import -D
 ou babylu@Project: ~$  npm install eslint-import-resolver-babel-plugin-root-import -D 
```

Enter fullscreen mode Exit fullscreen mode

在 *eslint* 配置文件中包含以下属性。

```
 "settings":  {  "import/resolver":  {  "babel-plugin-root-import":  {}  }  } 
```

Enter fullscreen mode Exit fullscreen mode

## 看似愚蠢实则不然的问题🤔

#### 我可以将它用于生产应用程序吗？

答:是的，如果您正确地遵循了这些步骤，您会看到我们在 babel.config.js 中为生产配置了

#### 我可以使用 React for web 吗？

回答:要使用 Web 的 babel 插件根导入，您需要执行一些其他设置

## 但并不是生活中的一切都是花朵🔴

你可能会在图书馆遇到 bug。如果你发现了它，请在官方的 babel 插件根导入库中报告，并帮助社区创建一个更好的库。

[https://github . com/entwicklerstube/babel-plugin-root-import/issues](https://github.com/entwicklerstube/babel-plugin-root-import/issues)

在推特上关注我

帮我把这篇文章翻译成其他语言。
如果您收到了翻译中的错误，请让存储库进行更正。我将非常感激。
[访问知识库](https://github.com/heybrunoandrade/my-articles/tree/master/Front-end/React%20Native/Absolute%20Imports)