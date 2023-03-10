# 将柏树添加到角形项目中

> 原文：<https://dev.to/christianmemije/adding-cypress-to-an-angular-project-30je>

> 将 Cypress 整合到一个有角度的项目中所需的步骤。

## 装柏

```
npm install cypress --D 
```

Enter fullscreen mode Exit fullscreen mode

## 使用 Nrwl 柏树生成器

使用 Nrwl Cypress builder 让`ng e2e`运行 Cypress 测试，而不是量角器测试。`@nrwl/cypress`取决于`@nrwl/workspace`。

```
npm install @nrwl/cypress --D
npm install @nrwl/workspace --D 
```

Enter fullscreen mode Exit fullscreen mode

对`angular.json`做如下修改。

*   将 e2e `builder`从`@angular-devkit/build-angular:protractor`更改为`@nrwl/cypress:cypress`。
*   移除`protractorConfig`选项。
*   将`cypressConfig`选项设置为`./cypress.json`。
*   将`tsConfig`选项设置为`./tsconfig.json`。

您的 e2e 配置应该看起来像下面这样:

```
{  "e2e":  {  "builder":  "@nrwl/cypress:cypress",  "options":  {  "devServerTarget":  "PROJECT_NAME:serve",  "cypressConfig":  "./cypress.json",  "tsConfig":  "./tsconfig.json"  },  "configurations":  {  "production":  {  "devServerTarget":  "PROJECT_NAME:serve:production"  }  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

## 添加 npm 脚本

添加 npm 脚本以便于运行 Cypress

```
{  "cypress:open":  "./node_modules/.bin/cypress open",  "cypress:run":  "./node_modules/.bin/cypress run"  } 
```

Enter fullscreen mode Exit fullscreen mode

## 设置初始柏树目录脚手架

运行`npm run cypress:open`第一次运行 Cypress，它在`/cypress`目录下进行初始搭建。

## 创建 Cypress 配置文件

用下面的代码在根级别创建一个`cypress.json`配置文件。

```
{  "baseUrl":  "http://localhost:4200/",  "integrationFolder":  "./cypress/integration/"  } 
```

Enter fullscreen mode Exit fullscreen mode

## 增加对 TypeScript 测试文件的支持

从技术上讲，这是可选的，因为您可以用 JavaScript 编写测试，但是 TypeScript 是令人敬畏的:D，所以让我们利用它。它需要安装以下两个依赖项。

```
npm install @cypress/webpack-preprocessor --D
npm install ts-loader --D 
```

Enter fullscreen mode Exit fullscreen mode

创建以下插件文件

`cypress/plugins/ts-preprocessor.js`

```
const wp = require('@cypress/webpack-preprocessor');

const webpackOptions = {
  resolve: {
    extensions: ['.ts', '.js'],
  },
  module: {
    rules: [
      {
        test: /\.ts$/,
        exclude: [/node_modules/],
        use: [
          {
            loader: 'ts-loader',
          },
        ],
      },
    ],
  },
};

const options = {
  webpackOptions,
};

module.exports = wp(options); 
```

Enter fullscreen mode Exit fullscreen mode

将`cypress/plugins/index.js`更新到以下以使用该插件。

```
const tsPreprocessor = require('./ts-preprocessor');

module.exports = on => {
  on('file:preprocessor', tsPreprocessor);
}; 
```

Enter fullscreen mode Exit fullscreen mode

## 为 Cypress 创建一个 tsconfig.json 文件

在`/cypress`目录中创建以下文件。

`cypress/tsconfig.json`

```
{  "extends":  "../tsconfig.json",  "include":  ["integration/*.ts",  "support/*.ts",  "../node_modules/cypress"]  } 
```

Enter fullscreen mode Exit fullscreen mode

## 更新你的。gitignore

将以下内容添加到您的`.gitignore`中，以防止查看赛普拉斯在测试期间拍摄的视频&截图。

```
cypress/videos/
cypress/screenshots/ 
```

Enter fullscreen mode Exit fullscreen mode

## 做一些量角器清理工作

如果您决定用 Cypress 完全替换量角器，不要忘记删除量角器作为一个依赖项，并删除任何量角器配置文件。

就这样吧！现在您应该已经准备好开始使用 Cypress 编写测试了！祝你好运！