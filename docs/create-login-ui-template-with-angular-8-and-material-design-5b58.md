# 用 Angular 8 和 Material Design 创建登录 UI 模板

> 原文：<https://dev.to/singhdigamber/create-login-ui-template-with-angular-8-and-material-design-5b58>

在这个 Angular 8 教程中，我们将学习用 Angular Material 8 构建漂亮的登录和注册 UI(用户界面)模板。我们将借助材料设计组件和 Angular 8 flex 布局 CDK 来创建登录和注册模板。我们将从头开始创建一个简单的 Angular 8 应用程序，并在其中实现一个登录和注册 UI 模块。你可以看看下面，我们将如何把一个简单的登录和注册 HTML 表单转换成一个漂亮的 UI 模板。

### 材料设计登录和注册 UI 模板演示

点击下面查看现场演示:

[登录 UI 演示](https://github-nofg3w.stackblitz.io)

您也可以通过点击下面的按钮获得 Git 回购:

[GitHub 回购](https://github.com/SinghDigamber/angular-material-login-template)

#### 目录

1.  先决条件
2.  设置角度 8 项目
3.  使用 Angular CLI 生成组件
4.  实施角形材料 8
5.  创建自定义角度材料模块
6.  初始化路由
7.  创建有角度的材质导航条
8.  使用材料设计创建注册用户界面
9.  结论

### 先决条件

首先，我们将从头开始安装和配置 Angular 8 项目。我假设您已经在系统中安装了 Node.js 和 Angular CLI。如果没有，按照本教程:[在 Mac OS 上安装节点 JS](https://dev.to/singhdigamber/how-to-install-node-js-on-mac-os-2d6a-temp-slug-3854940)

我使用下面的命令来安装 Angular CLI:

```
npm install @angular/cli -g 
```

### 设置角度 8 项目

在终端中输入命令并按 Enter 键创建一个新的 Angular 8 项目。

```
ng new angular-material-login-template

# ? Would you like to add Angular routing? = Yes
# ? Which stylesheet format would you like to use? = CSS 
```

进入项目文件夹:

```
cd angular-material-login-template 
```

### 使用 Angular CLI 生成组件

组件是 Angular 应用程序中一段可重用的代码。您可以使用单一指令快速建立元件。运行命令在 Angular 8 项目中创建登录和注册组件。

```
ng g component components/log-in --module app

ng g component components/register --module app 
```

`--module app`标签使得`app.module.ts`成为主应用模块文件。

### 实施有角材料 8

运行以下命令在 Angular 项目中安装 Angular Material 8 UI 库:

[点击此处阅读更多信息](https://www.positronx.io/create-login-ui-template-with-angular-8-material-design/)