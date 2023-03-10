# 用 Neutrino.js - Little Bits 构建 React 组件并部署到 NPM

> 原文：<https://dev.to/maciekgrzybek/build-and-deploy-to-npm-your-react-component-with-neutrino-js-little-bits-27o7>

##### Little Bits 是一系列短文，解释常见开发问题的快速解决方案。没有不必要的描述或代码片段。不废话。

在这篇短文中，我想向您展示如何轻松地设置您的开发环境来构建 React 组件，然后如何将它部署到 NPM，以便其他开发人员可以开始使用它。

#### 有用链接:

*   [中微子 JS](https://neutrinojs.org)
*   [NPM 出版文件](https://docs.npmjs.com/packages-and-modules/contributing-packages-to-the-registry)

#### 计划

1.  创建一个项目- yarn 或 npx
2.  构建一个组件或使用默认组件
3.  运行构建
4.  准备 package.json 文件
5.  部署到 NPM

### 1。创建一个项目- yarn 或 npx

```
yarn create @neutrinojs/project my-not-really-exciting-component 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
npx @neutrinojs/create-project my-not-really-exciting-component 
```

Enter fullscreen mode Exit fullscreen mode

之后中微子会问你关于你的项目。选择`Components`->-T1。下一步选择测试库(我通常选择 Jest)和林挺风格(Airbnb 或 StandardJS)。中微子将安装所有的依赖项并设置样板文件。

### 2。构建一个组件或使用默认组件

中微子为我们提供了一个简单的 HelloWorld 组件。我们将在这一步中使用它，但显然，在现实世界的场景中，构建一些很棒的东西。
提示:添加 readme.md 文件，其中包含安装、配置和使用代码的指导。
运行开发环境运行:

```
yarn start 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
npm start 
```

Enter fullscreen mode Exit fullscreen mode

这将与您的项目一起打开`localhost:5000`。

### 3。运行构建

如果您对您的组件运行满意:

```
yarn run build 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
npm run build 
```

Enter fullscreen mode Exit fullscreen mode

您的项目已经过优化，可以投入生产。

### 4。准备 package.json 文件

在部署我们的超级组件之前，我们需要向 package.json 添加一些细节

```
{  "name":  "my-not-really-exciting-component",  "version":  "1.0.0",  "description":  "My component, not really exicitng.",  "main":  "build/HelloWorld.js",  "author":  "Your Name <your_email@gmail.com>",  "repository":  {  "type"  :  "git",  "url"  :  "https://github.com/yourprofile/my-not-really-exciting-component"  },  "keywords":  [  "react",  "javascript",  "component"  ],  "license":  "MIT",  "scripts":  {  "build":  "neutrino build",  "start":  "neutrino start",  "lint":  "neutrino lint",  "test":  "neutrino test"  },  "devDependencies":  {  "@neutrinojs/airbnb":  "^8.3.0",  "@neutrinojs/jest":  "^8.3.0",  "@neutrinojs/react-components":  "^8.3.0",  "neutrino":  "^8.3.0",  "prop-types":  "^15.7.2",  "react":  "^16.9.0",  "react-dom":  "^16.9.0"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

### 5。部署到 NPM

在 [npm](https://www.npmjs.com/) 上创建一个帐户。之后，从你的终端简单地运行:

```
npm publish 
```

Enter fullscreen mode Exit fullscreen mode

### 总结

就这样，您已经成功地将 react 组件部署到了 NPM 注册中心。现在任何人都可以开始在他们的项目中使用它，你只需要准备好迎接所有开发人员的到来😎。