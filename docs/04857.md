# React-Native:对第三方包进行更改的 CI 友好方式

> 原文：<https://dev.to/waqqas/react-native-ci-friendly-way-to-make-changes-to-third-party-packages-5g1p>

你有没有遇到过这样的情况，你安装了一个包，却因为 RN 头文件的路径变了而无法与最新版本的 react-native 兼容？我也遇到过类似的问题，下面是我的解决方法。

我正在做一个 react-native 项目，想检测静音开关打开或关闭时的变化。像往常一样，我搜索并找到了一个漂亮的包 [`(react-native-silent-switch)`](https://github.com/gnestor/react-native-silent-switch) 来完成这项工作。正如文档中提到的，我在我的项目中添加了这一点，但遇到了障碍。由于头文件`RCTSilentSwitch.h`中的错误路径，它在编译期间出错。通常我会换成一些没有这个问题的类似的软件包，但在这种情况下，我找不到更好的替代方案。(也许我应该写一个)

我本来可以修改这个文件，但是它有两个问题。第一，任何其他开发人员都必须手动地在他们那边做这些更改，第二，我的 CI 构建会失败。构建会失败，因为该文件是通过`package.json`安装的节点包的一部分。

我需要一种方法，在`npm`安装完`RCTSilentSwitch.h`之后，在它开始编译之前修改它。我最初的反应是使用`post-install`阶段来修补文件，但是我想，可能也有适合它的东西。因此，我再次搜索，找到了另一个很酷的软件包。它被恰如其分地命名为 [`patch-package`](https://github.com/ds300/patch-package) 。

`patch-package`允许您以补丁的形式修改`node_modules`目录中的代码。用法也很简单。

我按照以下步骤让我的构建再次工作:

1.  按照[安装说明](https://github.com/ds300/patch-package#set-up)将`patch-package`安装为开发依赖项
2.  我更改了`RCTSilentSwitch.h`中所需的包含路径，并使用以下命令生成了一个补丁文件:

`yarn patch-package react-native-silent-switch`

在`patches`目录中生成了一个补丁文件。其内容如下

```
diff --git a/node_modules/react-native-silent-switch/RCTSilentSwitch.h b/node_modules/react-native-silent-switch/RCTSilentSwitch.h
index 61c955f..0adef09 100644 --- a/node_modules/react-native-silent-switch/RCTSilentSwitch.h +++ b/node_modules/react-native-silent-switch/RCTSilentSwitch.h @@ -1,4 +1,4 @@
-#import "RCTBridgeModule.h" +#import "React/RCTBridgeModule.h"
 #import "SharkfoodMuteSwitchDetector.h"

 @interface RCTSilentSwitch : NSObject <RCTBridgeModule> 
```

1.  我删除了`node_modules`目录并运行了`yarn install`,确认已经应用了补丁。它在最后打印了以下消息。

```
$ patch-package
patch-package 6.1.2
Applying patches...
react-native-silent-switch@0.1.0 ✔ 
```

## 结论

使用`patch-package`，我能够以一种 CI 友好的方式对`node_modules`目录进行小的修改。

虽然我对 react-native 包使用了这种方法，但它也可以用于安装在`node_modules`中的任何第三方包