# 从命令行创建 React 组件

> 原文：<https://dev.to/manindu/creating-react-components-from-command-line-26pm>

在本教程中，我将向您展示一种简单的方法来配置 React 项目，以便从命令行创建组件。

让我们从使用`create-react-app`创建一个新的 React 项目开始。

在您的终端中执行以下命令。这将创建一个名为 components-demo 的新项目。

```
manindu@dev:~$  create-react-app components-demo 
```

Enter fullscreen mode Exit fullscreen mode

接下来我们必须安装`create-react-component-folder`(由 SNR Seljan roddsson-[GitHub](https://github.com/snaerth)创建)NPM 包作为我们项目的开发依赖。顾名思义，这个包允许我们使用命令行创建组件。

```
manindu@dev:~$  npm i --save-dev create-react-component-folder 
```

Enter fullscreen mode Exit fullscreen mode

现在是时候创建一些组件了！！。我想创建一个名为 **components** 的目录，并在其中创建我的组件。我们可以通过执行下面的命令来实现。

```
manindu@dev:~$  npx crcf components/Button Input 
```

Enter fullscreen mode Exit fullscreen mode

我刚刚创建了两个组件，分别命名为**按钮**和**输入**。

```
 Button
  |_Button.css
  |_Button.js
  |_Button.test.js
  |_index.js 
```

Enter fullscreen mode Exit fullscreen mode

```
 Button
  |_Input.css
  |_Input.js
  |_Input.test.js
  |_index.js 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有两个组件。每个文件的 css 文件。然而，我更喜欢在我们的项目中使用带有 CSS 模块的 SASS。我们可以通过添加一个配置文件`create-react-component-folder`来实现

在项目根目录中，创建一个名为。并添加下面的代码。

```
 [
    "scss",
    "cssmodules"
  ] 
```

Enter fullscreen mode Exit fullscreen mode

删除我们之前创建的**按钮**和**输入**组件，并运行如下相同的命令。

```
 manindu@dev:~$  npx crcf components/Button Input 
```

Enter fullscreen mode Exit fullscreen mode

这一次，您将获得带有. module.scss 文件的相同组件，这意味着现在您可以使用带有 css 模块的 SASS 进行样式化(您必须安装 node-sass 才能使用 sass)

```
 Button
  |_Button.js
  |_Button.module.scss
  |_Button.test.js
  |_index.js 
```

Enter fullscreen mode Exit fullscreen mode

```
 Button
  |_Input.js
  |_Input.module.scss
  |_Input.test.js
  |_index.js 
```

Enter fullscreen mode Exit fullscreen mode

你可以在他们的[文档](https://www.npmjs.com/package/create-react-component-folder)中读到更多关于`create-react-component-folder`的内容。

编码快乐！！！