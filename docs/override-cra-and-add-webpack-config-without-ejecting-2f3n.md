# 覆盖 CRA 并添加 webpack 配置，而不弹出。

> 原文：<https://dev.to/nodewarrior/override-cra-and-add-webpack-config-without-ejecting-2f3n>

我将直接进入过程。Webpack 很牛逼，create-react-app 也是。弹出 CRA 来配置 webpack 是一个挑战，即使对大多数专业开发人员来说也是如此。我是一个傻瓜，我想找出一种有效的方式来配置和共享，以帮助其他开发人员。

*   创建一个反应应用程序，用你的创造力命名。

```
$  npm install -g create-react-app
$  create-react-app my-app
  $  cd my-app 
```

Enter fullscreen mode Exit fullscreen mode

*   在这里，我们必须定制 webpack 配置。通常我们不使用 CRA，而是使用一个很棒的库，叫做 react-app-rewired with customize-CRA。为什么我们需要两个库？让我们安装 react-app-rewired 并定制-cra

注意——不要惊慌，它不会给你的代码增加任何噪声。

```
$  npm install react-app-rewired customize-cra --save-dev 
```

Enter fullscreen mode Exit fullscreen mode

你必须对你的包做一些修改

```
/* package.json */
"scripts": {
-   "start": "react-scripts start",
+   "start": "react-app-rewired start",
-   "build": "react-scripts build",
+   "build": "react-app-rewired build",
} 
```

Enter fullscreen mode Exit fullscreen mode

*   创建一个 config-overrides.js 文件，这将处理您的所有 webpack 配置。这是一个关于我如何使用它的例子，添加你自己的插件，预置和导入，就像我们在原生 webpack 中做的那样。

注意-嘿，如果你在导入两个库时发现任何困难，请使用我的实现。在导入之前，你需要 babel-plugin-import。这是我们的 Babel 插件，用于按需导入组件。

```
$  npm install babel-plugin-import --save-dev 
```

Enter fullscreen mode Exit fullscreen mode

我在用本地方式做这件事时遇到了问题:

```
fixBabelImports('import', {
+     libraryName: 'antd-mobile',
+     style: 'css',
+   }), 
```

Enter fullscreen mode Exit fullscreen mode

```
const {
  override,
  addLessLoader,
  disableChunk,
  addBabelPlugins,
  removeModuleScopePlugin,
  addBabelPresets
} = require("customize-cra");

module.exports = override(
  ...addBabelPresets("@babel/preset-env", "@babel/preset-react"),
  ...addBabelPlugins(
    [
      "import",
      { libraryName: "antd", libraryDirectory: "lib", style: true },
      "antd"
    ],
    [
      "import",
      { libraryName: "antd-mobile", libraryDirectory: "lib", style: "css" },
      "antd-mobile"
    ]
  ),
  addLessLoader({
    javascriptEnabled: true,
    importLoaders: true,
    modifyVars: {}
  }),
  disableChunk(),
  removeModuleScopePlugin()
); 
```

Enter fullscreen mode Exit fullscreen mode

*   启动应用程序，瞧！，它应该是好的并且正在工作。

```
$  cd my-app
$  npm start 
```

Enter fullscreen mode Exit fullscreen mode

* * *

有关 react-app-rewired 或 customize-cra 的任何进一步信息，请查看这些链接和他们令人敬畏的 library[^1 的荣誉