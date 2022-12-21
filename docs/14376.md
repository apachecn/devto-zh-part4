# 用 Ember JS 制作电子应用程序第 2 部分:MacOS

> 原文：<https://dev.to/mitchartemis/making-an-electron-app-with-ember-js-part-2-macos-3pae>

这是“用 Ember JS 制作电子应用”系列博文的第二部分，我将回顾我是如何为 web、Mac、Windows 和 Linux 开发我的应用程序的。

这篇文章假设你已经阅读了第一部分，如果你还没有，那么我强烈推荐它。现在，继续表演！

## 为 MacOS 搭建

在构建应用程序之前，我们需要为代码签名做准备。为此，你需要有一个苹果开发者账户，如果你还没有的话，你可以从[苹果开发者网站](http://developer.apple.com)获得。请注意，这需要 99 美元的年费。

为什么代码签名很重要？很高兴你问了！代码签名确保用户下载的文件没有被篡改，并且来自您期望的开发人员。如果没有它，MacOS 和 Windows 会警告用户运行该应用程序，并在一定程度上阻止他们这样做。

出于教育目的，您应该能够在没有代码签名的情况下继续学习，但是对于生产应用程序，我强烈建议您这样做。

一旦你设置了开发者账户，创建一个“开发者 ID 应用”证书，下载并安装在你的 Mac 电脑上。

在`ember-electron/electron-forge-config.js`中添加以下内容:

```
// At the top
const path = require('path');
const rootPath = path.join('./');

function getCodesignIdentity() {
  if (process.platform !== 'darwin') {
      return;
  }

  if (process.env.CODESIGN_IDENTITY) {
      return process.env.CODESIGN_IDENTITY;
  } else {
      console.log('Codesigning identity can not be found, release build will fail');
      console.log('To fix, set CODESIGN_IDENTITY');
  }
}

function getBundleId() {
  if (process.platform !== 'darwin') {
      return;
  }

  if (process.env.BUNDLE_ID) {
      return process.env.BUNDLE_ID;
  } else {
      console.log('bundle id can not be found, release build will fail');
      console.log('To fix, set BUNDLE_ID');
  }
}

// Replace electronPackagerConfig with this
"electronPackagerConfig": {
  "packageManager": "yarn",
  name: "Shopper",
  icon: path.join(rootPath, 'ember-electron', 'resources', 'shopper'),
  versionString: {
    CompanyName: 'Acme Ltd',
    FileDescription: 'Shpoper for Desktop',
    ProductName: 'Shopper',
    InternalName: 'Shopper'
  },
  "osxSign": {
    "identity": getCodesignIdentity()
    },
    "appBundleId": getBundleId(),
    "appCategoryType": "app-category-type=public.app-category.developer-tools",
  },

// At the bottom of module.exports
electronInstallerDMG: {
    title: 'Shopper',
    icon: path.join(rootPath, 'ember-electron', 'resources', 'shopper.icns'),

    iconsize: 100,
    window: {
        size: {
            width: 600,
            height: 571
        }
    }
}, 
```

在我们能跑之前还有一步。Mac 上的代码签名[不再允许应用捆绑包中的任何文件拥有包含资源分支或 Finder 信息的扩展属性。这很可能适用于你已经创建的任何资产，你可以通过运行
来调试它](#)

```
xattr -lr . 
```

在购物者应用程序中，它只影响新创建的图标。我们可以通过运行以下命令来解决这个问题。在您自己的应用程序中，您需要使用这两个命令来查找和修复任何资产。如果不这样做，您的应用程序将会构建，但是代码签名可能会失败。

```
xattr -cr ember-electron/resources 
```

现在是有趣的部分。使用下面的构建命令并更新`CODESIGN_IDENTITY`和`BUNDLE_ID`变量。捆绑 ID 应该是一个唯一的代码，大多数人使用他们的域名与唯一的子域相反。

运行这个命令，去给自己冲杯热茶，当你回来的时候，你应该在`electron-out/make/`文件中有一个`.zip`包含这个应用程序。

`env CODESIGN_IDENTITY="Developer ID Application: <Name> (<ID>)" env BUNDLE_ID="io.exampledomain.desktop" ELECTRON_ENV=production ember electron:make --environment=production `

解压并运行它，你应该会看到新的应用程序，停靠图标和所有！

## 创建 DMG 安装程序

一个可选的好办法是创建一个 DMG 文件，引导用户将你的应用程序移动到他们的`/Applications`目录中。

为此，我使用了一个名为 [create-dmg](https://github.com/sindresorhus/create-dmg) 的开源工具。它使用起来相当简单，会自动获取您的代码签名证书。

```
cd electron-out/make/
rm -rf Shopper.app
unzip Shopper-darwin-x64-0.1.0.zip
create-dmg Shopper.app ./ 
```

## 迄今为止我们所做的

我们已经配置了电子应用程序来生成代码签名的 MacOS 应用程序，并将其捆绑到一个易于安装的 DMG 文件中。

在下一章中，我们将看一下 MacOS 应用程序的注释。