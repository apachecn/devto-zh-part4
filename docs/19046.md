# 共享更漂亮的配置文件

> 原文：<https://dev.to/matthias/sharing-prettier-configuration-files-gba>

我在几个项目中使用了更漂亮的。对于那些不知道什么是更漂亮的人来说，它是一个固执己见的代码格式化程序，支持。漂亮的语言支持 JavaScript(包括 ES2017)、JSX、Angular、Vue、Flow、TypeScript、CSS(包括 Less 和 SCSS)、HTML、JSON、GraphQL、Markdown(包括 GFM 和 MDX)和 YAML。

漂亮只有几个选项来配置，如制表符或空格，打印宽度或报价风格。完整的选项列表可在[更漂亮的文档](https://prettier.io/docs/en/options.html)中找到。

当我开始创建更多的项目时，我寻找一种方法来共享更漂亮的配置文件。幸运的是，这个功能已经得到了更漂亮的支持。

你只需要发布一个模块，导出你定制的更漂亮的配置。

新建一个目录，创建两个文件:`package.json`和`index.json`。`package.json`是你的模块的清单，它包含元数据、依赖项和脚本。如果你想把你的模块发布到 NPM，这是必须的。你更漂亮的配置进了`index.json`。

这是我的文件内容:

`index.json` :

```
{  "semi":  true,  "singleQuote":  true,  "tabWidth":  2,  "trailingComma":  "none",  "printWidth":  100  } 
```

Enter fullscreen mode Exit fullscreen mode

`package.json` :

```
{  "name":  "@your-name-here/prettier-config",  "version":  "1.0.1",  "description":  "Prettier Config",  "main":  "index.json",  "license":  "MIT",  "keywords":  [  "prettier",  "prettier-config"  ],  "repository":  {  "type":  "git",  "url":  "https://github.com/your-name-here/prettier-config"  },  "homepage":  "https://github.com/your-name-here/prettier-config"  } 
```

Enter fullscreen mode Exit fullscreen mode

你现在可以在你的模块目录中运行`npm publish`，让每个人都可以使用你更漂亮的配置。

如果您想在其他项目中使用您共享的更漂亮的配置，您需要通过添加`prettier`属性:
在您的项目的`package.json`清单中引用它

```
{  "name":  "my-cool-library",  //  ...  "prettier":  "@your-name-here/prettier-config"  //  ...  } 
```

Enter fullscreen mode Exit fullscreen mode

此外，您应该删除所有其他更漂亮的配置，以避免您的共享配置文件被覆盖。

* * *

如果你喜欢我的内容，你可能想在 Twitter 上关注我？！ [@fullstack_to](https://twitter.com/fullstack_to)