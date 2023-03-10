# 公证你的电子应用程序

> 原文：<https://dev.to/pimterry/notarizing-your-electron-app-4gih>

在 macOS 的下一个版本(10.15)中，如果你的应用没有经过公证，你的用户就不能运行它。如果你在分发 Mac 应用，你需要处理这个问题。幸运的是，对于电子应用程序来说，这个过程相当简单。

10.15 的最终发布日期还没有宣布，但有许多测试版可供开发者测试。它计划在 2019 年秋季推出，在 10.14.5 中，公证的硬性要求已经成为所有新开发者账户(即从未分发过与其 Apple 开发者 id 相关联的软件的任何人)的现实。

## 什么是公证？

公证旨在让苹果公司快速确保你的软件在用户的电脑上运行是安全的。完整的细节可以在苹果自己的开发者文档中找到。

简而言之，这是一个过程:

*   你把你的新应用上传到苹果的公证服务，
*   公证服务会自动检查它是否有恶意内容和其他问题
*   公证服务会返回给您一张票，显示已经对这个构建进行了检查
*   你把这张票贴在软件上
*   当应用程序安装后，用户 MAC 电脑上的看门人软件可以检查装订的票据，从而知道该软件已经过苹果公司的检查。

此外，这意味着您的软件的每个可分发版本都附带一个标签，如果需要，可以在以后撤销。如果您发现恶意代码以某种方式进入了您的应用程序，或者您的签名密钥已经泄露，其他人正在分发未经授权的版本，您可以远程撤销票证并使该软件不可安装。

此外，公证的要求比现有的代码签名限制更严格。由于公证现在是强制性的，这有效地代表了苹果收紧他们的应用程序安全规则。

请注意，这是*而不是*应用审查:这里没有人工流程，它是对您的可分发应用版本的自动扫描，以及对这些扫描的审计跟踪。

## 涉及到什么？

典型的电子应用程序构建需要几个主要步骤:

1.  确保您的应用版本符合公证要求。这意味着您需要:
    *   在 macOS 10.12+上使用 Xcode 10+构建
    *   基于 macOS 10.9 或更高版本的 SDK 构建
    *   用你的开发者 ID 对你的构建进行代码签名(本地开发或 Mac 发行证书是不够的)
    *   在您的代码签名中包含一个安全时间戳(在大多数情况下，这已经自动发生了)
    *   启用“强化运行时”功能
    *   给你的应用赋予`com.apple.security.cs.allow-unsigned-executable-memory`权限，否则当强化运行时启用时，电子将无法运行
2.  在发布之前公证您的所有版本:
    *   构建可执行文件，但不要打包到 DMG/etc 中
    *   将应用构建提交给苹果的公证服务
    *   等待公证服务给你一张票
    *   将它附加到可执行文件中
    *   继续您的打包流程

## 在实践中我如何做到这一点？

如果您想要一个工作示例，我最近在 [HTTP 工具包](https://httptoolkit.tech)中添加了公证，您可以在这里看到所涉及的提交:

*   [更新至 XCode 10](https://github.com/httptoolkit/httptoolkit-desktop/commit/d8c55a6b42fa9ab67475c03cd497d8eb6d0d5d90)
*   [完成公证要求](https://github.com/httptoolkit/httptoolkit-desktop/commit/c67896837fb50cb635a0a9589052e4fafc48dd64)
*   [启用公证](https://github.com/httptoolkit/httptoolkit-desktop/commit/956327cad3a6d2367470fc7a4ffb6600d8cc7c28)

让我们一步一步地了解一个用 Electron Forge v5 构建的典型应用程序。我假设您已经设置了代码签名，但没有其他内容，并且您正在 Travis 上构建应用程序。如果您不使用 Travis，这应该很容易转换到其他环境，但是如果您没有代码签名，您必须首先设置它。

*   确定您使用的是 OSX 10.12+和 Xcode 10+
    *   对于 travis 来说，只需要将`osx_image`设置为至少`xcode10`即可。
*   记录所需的 Apple ID 登录详细信息
    *   将您的用户名(您的 Apple 开发者帐户电子邮件地址)保存在名为`APPLE_ID`的安全环境变量中。
    *   按照[https://support.apple.com/en-us/HT204397](https://support.apple.com/en-us/HT204397)的指示，为你的开发者账户创建一个应用专用密码。
    *   将特定于应用程序的密码存储在名为`APPLE_ID_PASSWORD`的安全环境变量中。
*   在您的[电子-osx-sign](https://github.com/electron/electron-osx-sign) 配置中设置`hardened-runtime: true`和`gatekeeper-assess: false`
    *   对于电子锻造 v5，这是在`electronPackagerConfig`内`osxSign`下的锻造配置中。
    *   `hardened-runtime`足够清楚:这使得[能够强化运行时](https://developer.apple.com/documentation/security/hardened_runtime_entitlements)。
    *   禁用`gatekeeper-assess`是必需的，因为否则 e-OS x-sign 将要求看门人对构建进行完整性检查，而在新的 MacOS 版本中，这将失败，因为它还没有经过公证。幸运的是，公证会在以后为我们做同样的检查，所以跳过这个是安全的。
*   创建一个权限文件，并设置电子 osx-sign 的`entitlements`和`entitlements-inherit`配置属性来使用它
    *   在与上一步相同的`osxSign`配置中，`entitlements`和`entitlements-inherit`配置属性应该是该文件的相对路径(如`src/entitlements.plist`)。
    *   电子应用程序的最小权限文件如下所示:

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
    <dict>
        <key>com.apple.security.cs.allow-unsigned-executable-memory</key>
        <true/>
    </dict>
</plist> 
```

*   安装[电子公证](https://www.npmjs.com/package/electron-notarize)
*   创建将执行公证的脚本
    *   这需要从电子公证中调用`notarize`函数，并等待它完成。
    *   示例脚本可能如下所示(您需要更新`buildOutput`和`appBundleId`以匹配您自己的应用程序):

```
const { notarize } = require('electron-notarize');

// Path from here to your build app executable:
const buildOutput = require('path').resolve(
    __dirname,
    '..',
    'out',
    'HTTP Toolkit-darwin-x64',
    'HTTP Toolkit.app'
);

module.exports = function () {
    if (process.platform !== 'darwin') {
        console.log('Not a Mac; skipping notarization');
        return;
    }

    console.log('Notarizing...');

    return notarize({
        appBundleId: 'tech.httptoolkit.desktop',
        appPath: buildOutput,
        appleId: process.env.APPLE_ID,
        appleIdPassword: process.env.APPLE_ID_PASSWORD
    }).catch((e) => {
        console.error(e);
        throw e;
    });
} 
```

*   在构建可执行文件之后，但在打包成 DMG 或类似文件之前，运行这个脚本。
    *   令人困惑的是，正确的锻造钩叫做`postPackage`。
    *   要在 Electron Forge v5 中进行设置，您需要在 Forge 配置的顶层添加以下内容:

```
"hooks": {
    "postPackage": require("./src/hooks/notarize.js")
} 
```

## 获取公证！

一旦这个到位，你的构建应该立即开始公证你的 OSX 电子可执行文件。每次公证完成后，您都会收到一封来自苹果公司的电子邮件；这些可能对你的过程中的审计公证有用，但是它们可能非常嘈杂，所以你可能想把它们从你的收件箱中过滤掉。

你可以通过在 Mac 上打开生成的应用程序来检查公证是否有效；下载后第一次运行时，您应该会看到一个弹出窗口，显示如下内容:

> “YourApp.App”是从网上下载的 app。您确定要打开它吗？
> Chrome 于 2019 年 8 月 6 日下载了该文件。
> **苹果对其进行了恶意软件检查，没有发现任何恶意软件。**

最后一行是这里的关键:您的 Mac 已经检测到了装订的票证，它对此感到满意，您一切都好。

但是，不要忘记实际运行应用程序，并确认它在严格的运行时要求下工作正常！如果你在这方面有任何问题，你可能想看看包括[额外的权利](https://developer.apple.com/documentation/bundleresources/entitlements)，或者减少你对受保护的 API 的使用。

最后一个注意事项:如果您从互联网下载构建版本，例如从您的 CI 构建输出中下载，您将会看到上面的消息。如果你是本地建的，或者因为其他一些原因需要手动确认公证，那就看看[苹果的看门人测试说明](https://developer.apple.com/library/archive/documentation/Security/Conceptual/CodeSigningGuide/Procedures/Procedures.html#//apple_ref/doc/uid/TP40005929-CH4-SW25)。

就是这样！祝你好运，公证愉快。

*原载于 [HTTP Toolkit 博客](https://httptoolkit.tech/blog/notarizing-electron-apps-with-electron-forge/)*