# 用 Ember JS 制作电子应用程序第 2.5 部分:MacOS 公证

> 原文：<https://dev.to/mitchartemis/ember-electron-macos-2-5-notarization-4jfd>

这是博客系列文章“用 Ember JS 制作电子应用程序”的第 2 . 5 部分，我回顾了我是如何为 web、Mac、Windows 和 Linux 开发应用程序的。

随着 MacOS 即将更新到 10.15 Catalina，重要的是不要升级你的应用程序，否则你的用户将无法安装它！我在之前的文章中没有提到这个问题，但是我觉得这个问题需要解决。

## 什么是公证？

公证是 MacOS 生态系统的一项新功能，是在 App Store 之外发布的应用程序所必需的。这项功能允许苹果扫描你的软件中的恶意内容，以便用户可以放心使用你的应用程序。不仅如此，如果你的签名密钥被暴露，你可以联系苹果公司，禁止你的应用程序的未授权版本被打开。

你可以在[苹果开发者网站](https://developer.apple.com/documentation/security/notarizing_your_app_before_distribution)上了解更多相关信息。

## 记录我们的应用程序

有一些变化需要作出，以提高我们的应用程序。

首先，我们需要将`electron-notorize`添加到`package.json`

```
+ "electron-notarize": "^0.1.1" 
```

接下来，在我们的`ember-electron/electron-forge.config.js`文件中，我们需要添加以下代码更改。

```
+ const { notarize } = require('electron-notarize'); // ...

"osxSign": {
-     "identity": getCodesignIdentity() +    "identity": getCodesignIdentity(),
+    "gatekeeper-assess": false,
+     "hardened-runtime": true,
+     "entitlements": path.join(rootPath, "ember-electron", "resources-darwin", "entitlements.plist"),
+     "entitlements-inherit": path.join(rootPath, "ember-electron", "resources-darwin", "entitlements.plist") },
// ...
+  "hooks": {
+    postPackage: async function () {
+      if (process.platform !== 'darwin') {
+          console.log('Skipping notarization - not building for Mac');
+          return;
+      }
+
+      console.log('Notarizing...');
+
+      await notarize({
+          appBundleId: getBundleId(),
+          appPath: path.join(rootPath, "electron-out", "Shopper-darwin-x64", "Shopper.app"),
+          appleId: process.env.APPLE_ID,
+          appleIdPassword: process.env.APPLE_ID_PASSWORD
+      }); 
+    } 
```

这些变化有什么作用？首先，需要禁用 gatekeeper 才能正常工作，我们需要指定一个接下来要创建的`entitlements.plist`文件。`postPackage`钩子处理公证。我们检查是否在为 MacOS 编译，然后运行公证流程。

您需要将`Shopper`引用更改为您自己的应用程序名称。

我们还指定了两个需要传递给构建命令的新环境变量，`APPLE_ID`和`APPLE_ID_PASSWORD`。该密码是特定于应用程序的，可以从您在 https://appleid.apple.com-**的账户中生成。请不要在此使用您真实的 Apple ID 密码！**。更多细节见这些说明[https://support.apple.com/en-us/HT204397](https://support.apple.com/en-us/HT204397)

接下来，是时候创建授权文件了，创建`ember-electron/resources-darwin/entitlements.plist`并添加下面的

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

现在我们可以构建新的版本了。请注意，这可能需要一些时间，因为应用程序会在苹果的服务器上进行检查。记得将环境变量更改为您自己的环境变量。

```
env CODESIGN_IDENTITY="Developer ID Application: <Name> (<ID>)" env BUNDLE_ID="io.exampledomain.desktop" ELECTRON_ENV=production env APPLE_ID_PASSWORD=<password> env APPLE_ID=<appleid> ember electron:make --environment=production 
```

## dmg 文件

对于`.dmg`文件不再需要代码签名，因为苹果现在检查其中的`.app`文件。考虑到这一点，我们不能再使用`create-dmg` Javascript 包，因为它会自动找到您的证书并将其应用到`.dmg`构建中。

相反，我们可以使用已经存在于依赖关系中的`electron-installer-dmg`。

```
# Unzip the generated app
unzip Shopper-darwin-x64-x.x.x.zip

# Generate the dmg installer
./node_modules/.bin/electron-installer-dmg ./electron-out/make/Shopper.app Shopper --out=./electron-out/make/ --icon=./electron-assets/shopper.icns --icon-size=100 
```

这就是全部了！

在下一章中，我们将看看 Linux 的构建！