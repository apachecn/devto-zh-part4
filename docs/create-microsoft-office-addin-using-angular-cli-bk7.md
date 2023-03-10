# 使用 Angular CLI 创建 Microsoft Office 加载项

> 原文：<https://dev.to/igagrock/create-microsoft-office-addin-using-angular-cli-bk7>

如果您正在阅读本教程，您可能是正在尝试使用 Angular CLI 创建 Microsoft office 插件的开发人员之一。

Microsoft 通过一个用于 VS 代码编辑器的 yeoman generator 提供了使用 angular 创建外接程序的完整过程。这种方法工作良好，但它使用手动 webpack 配置 angular。我还面临以下问题:

*   **无法在角度组件中使用模板 URL**
*   **不支持每个角度组件的 CSS 文件**

本教程的目标是演示一种使用 [angular@8.0.0](mailto:angular@8.0.0) CLI 创建项目结构的方法，并对其进行配置，使其为开发 office 插件做好准备。

*请注意，它仍然不支持微软提供的`office-addin-debugger`插件。我把它作为一个 npm 开发依赖项添加到这里，我还在试图弄清楚如何让它工作。但是您可以在 outlook web 等 web 应用程序上调试加载项。*

> 我假设你已经安装了`node`和`npm`

**让我们开始吧—**

## 使用约曼生成器生成 Office 插件 angular app

Office [官方文件](https://docs.microsoft.com/en-us/outlook/add-ins/quick-start?tabs=visual-studio-code)已经提供了详细的步骤。我将只提供简明的步骤。

*   在命令行中运行以下命令。它将安装办公室约曼发电机。

```
npm install -g yo generator-office 
```

*   使用下面的命令生成 office add angular 项目结构。

```
yo office --skip-install 
```

上述命令将提供交互式输入。选择以下选项

```
 Choose a project type: (Use arrow keys) 
   // choose (2) Office Add-in Task Pane project using Angular framework

   Choose a script type: (Use arrow keys)
   // choose (1) Typescript

   What do you want to name your add-in? (My Office Add-in) 
   //Give any name you want. for this tutorial, I would keep demo-addin

   Which Office client application would you like to support? 
   //choose (3) Outlook 
```

注意`--skip-install`参数。嗯，我们不希望`npm`在这里安装所有的依赖项。我们只需要`manfiest.xml`文件和一些来自 package.json 的微软 office 依赖项

我们的 outlook addin 项目结构是使用 yeomen generator 完成的。下面是生成的项目的结构。

```
.
    ├── assets
    │   ├── icon-16.png
    │   ├── icon-32.png
    │   ├── icon-80.png
    │   └── logo-filled.png
    ├── CONTRIBUTING.md
    ├── LICENSE
    ├── manifest.xml
    ├── package.json
    ├── package-lock.json
    ├── README.md
    ├── src
    │   ├── commands
    │   └── taskpane
    ├── tsconfig.json
    └── webpack.config.js 
```

## 使用 Angular CLI 创建角度应用程序

*   安装角度 CLI

```
npm install -g @angular/cli 
```

*   生成角度应用程序

```
ng new demo-angular-addin 
```

*   运行`ng serve`以确保应用程序正在运行

我们的 angular 应用程序的结构如下所示:

```
.
      ├── angular.json
      ├── e2e
      ├── node_modules
      ├── package.json
      ├── package-lock.json
      ├── README.md
      ├── src
      ├── tsconfig.json
      └── tslint.json 
```

## 将插件清单复制到 angular 应用程序

从我们使用 yeomen generator 生成的 demo-addin 中复制`manifest.xml`并粘贴到 angular 应用程序的根文件夹中。

## 改变 angular 应用默认端口号

注意，`manifest.xml`拥有端口 3000 的所有映射，比如`localhost:3000`。此外，angular 应用程序的默认端口是 4200。我们要么必须更改清单中的端口，要么更改 angular 服务器端口。无论哪种方式，都应该有效。

让我们将 angular 应用程序的默认端口(4200)更改为 3000。打开`angular.json`文件，查找`server`键，更改默认端口。下面是一个例子。

```
"serve": {
     "builder": "@angular-devkit/build-angular:dev-server",
     "options": {
         "browserTarget": "demo-angular-addin:build",
         "port": 3000
    },
    "configurations": {
                "production": {
        "browserTarget": "demo-angular-addin:build:production",
        "port": 3000
                }
    }
   }, 
```

运行`ng-serve`来验证端口。

## 将生成的 office 插件中的默认图标复制到 angular app

提供的默认图标在外接程序清单中配置。加载外接程序时将显示这些图标。

如果两个项目在同一个文件夹中，请使用下面的命令或手动操作。

```
cp -r demo-addin/assets/* demo-angular-addin/src/assets/ 
```

## 更新清单映射-替换为 index.html 页面

在`manifest.xml`中，对于`taskpane.url`的每一次出现或者 URL 类似`https://localhost:3000/taskpane.html`的任何其他位置，将**taskpane.html**更新为**index.html**。您也可以删除 taskpane.html，只保留 host: port (localhost:3000)。

## 为角度应用配置 SSL

Microsoft Office 加载项必须通过`https`连接提供。我们需要生成一个 SSL 证书和密钥，并将我们的 angular 应用程序配置为通过`https`提供服务。

转到标题为[使用可信证书在 HTTPS 上运行 Angular CLI](https://medium.com/@rubenvermeulen/running-angular-cli-over-https-with-a-trusted-certificate-4a0d5f92747a)的教程。它包含了在 angular 应用程序中配置 SSL 的所有步骤。

如果您仍然面临任何问题，请尝试通过选择`local machine`来安装证书。并直接在终端中使用 SSL 选项。

```
ng serve --ssl true --ssl-cert "ssl/server.crt" --ssl-key "ssl/server.key" 
```

您也可以在脚本内部的`package.json`中配置相同的命令——例如:

```
"start-ssl" : "ng serve --ssl true --ssl-cert \"ssl/server.crt\" --ssl-key \"ssl/server.key\"" 
```

## 将 Typescript 编译器目标类型改为 es5 而不是 es2015

当我测试桌面应用程序的 Outlook 插件时，它没有加载角度组件。然而，它在 outlook web app 中运行良好。我最初发布了一个 [stackOverFlow 问题](https://stackoverflow.com/questions/56600059/microsoft-office-addin-for-outlook-does-not-load-on-outlook-desktop-app)。经过彻底的调试，我发现 Outlook 桌面应用程序还不支持`es2015`。

让我们更新`tsconfig.app.json`并在`compilerOptions`下添加新目标。

```
"target": "es5" 
```

## 使用 npm 添加缺失的依赖项和开发依赖项

##### npm 依赖关系

```
npm install --save @microsoft/office-js @microsoft/office-js-helpers@^1.0.1 office-ui-fabric-js@^1.3.0 
```

我还添加了`office-js`作为 npm 依赖项。但是，`office js`不是一个有效的 es6 模块，因此不能这样使用。我们仍然需要使用来自 CDN 的脚本标签来加载它。我们将在教程的后面讨论它。你也可以在这里跟踪本期[。如果将来它被更新为 es6 模块，您可以通过导入组件而不是从 CDN 加载来直接使用它。](https://github.com/Microsoft/TypeScript/issues/11420)

##### npm 依赖项

```
npm install --save-dev @types/office-js@^1.0.1 @types/office-runtime@^1.0.7 office-addin-debugging@^2.1.13 office-addin-dev-certs@^1.0.1 office-toolbox@^0.1.1 
```

##### 添加缺失的类型

在编译期间，Typescript 编译器会抱怨缺少类型。因为我们将在开发期间使用 Microsoft Office APIs，所以我们需要将 office-js 添加到类型中。

更新`tsconfig.app.json`并在`types`数组下添加`office-js`。

```
"types": [
      "office-js"
   ] 
```

## 从 taskpane.html 复制剧本到 index.html

如前所述，Office-js 必须从 CDN 添加。如果您打算使用 office `fabric-ui`来开发 addin 用户界面，请同时包含来自 CDN 的 fabric 库。

```
<!-- Office JavaScript API -->
   <script type="text/javascript" src="https://appsforoffice.microsoft.com/lib/1.1/hosted/office.js"></script>

   <!-- For more information on Office UI Fabric, visit https://developer.microsoft.com/fabric. -->
   <link rel="stylesheet" href="https://static2.sharepointonline.com/files/fabric/office-ui-fabric-js/1.4.0/css/fabric.min.css" />
   <link rel="stylesheet" href="https://static2.sharepointonline.com/files/fabric/office-ui-fabric-js/1.4.0/css/fabric.components.min.css" />

   <script src="https://static2.sharepointonline.com/files/fabric/office-ui-fabric-js/1.4.0/js/fabric.min.js"></script> 
```

## 自举`Office.initialize`函数内的角度应用。

确保在 Office 应用程序完全加载后加载外接程序。

让我们通过将引导代码包装在`initialize`函数中来更新`main.ts`文件。

```
Office.initialize = reason =>{
       platformBrowserDynamic().bootstrapModule(AppModule)
                 .catch(err => console.error(err));
   }; 
```

现在唯一剩下的事情就是加载我们的微软 Outlook 插件。你可以在官方文档[这里](https://docs.microsoft.com/en-us/office/dev/add-ins/testing/create-a-network-shared-folder-catalog-for-task-pane-and-content-add-ins)找到。

-
最初发表于 2019 年 6 月 16 日[https://www.initgrep.com](https://www.initgrep.com/posts/javascript/angular/microsoft-office-addin-using-angular-cli)。