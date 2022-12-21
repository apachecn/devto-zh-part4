# VS 代码配置文件切换器扩展支持

> 原文：<https://dev.to/azure/vs-code-profile-switcher-extensions-support-4b4f>

我刚刚发布了我的 [VS 代码配置文件切换器扩展](https://marketplace.visualstudio.com/items?itemName=aaronpowell.vscode-profile-switcher&WT.mc_id=devto-blog-aapowell)的一些更新，增加了我第一次发布时最需要的特性，扩展支持！

## 分机支持

当创建一个配置文件时，你可能想要加载不同的扩展，比如你正在做一些 React 工作，也许你不想加载 Vue 扩展，你安装的扩展越多，VS 代码就越需要激活，所有这些都需要时间。

现在，当您保存一个配置文件(新的或覆盖现有的)时，扩展将查看您拥有的所有扩展，并将它们添加到您的扩展配置文件中。然后，当您下次加载该概要文件时，扩展将查看 VS 代码中加载了哪些扩展，将其与概要文件指出您应该拥有的扩展列表进行比较，并安装缺少的扩展，同时删除多余的扩展。

这与处理设置的方式略有不同，设置是**附加的**，这意味着配置文件中的任何设置都将合并到现有设置之上，但这似乎不适用于扩展。

## 性能考虑

我会经常在多个配置文件之间切换，所以我想确保它能体面地运行。为此，当一个配置文件删除一个扩展时，它会将其移动到 [`globalStorage`](https://code.visualstudio.com/api/references/vscode-api?WT.mc_id=devto-blog-aapowell#ExtensionContext.globalStoragePath) ，然后当再次需要它时，它会将其复制回来。如果扩展不存在于`globalStorage`中，它将从市场中安装它。从市场上安装确实需要时间，如果你有很多扩展需要从市场上安装，时间会更长，但是不要太频繁。

## 忽略扩展名

可能会有你总是想要安装的扩展，比如你喜欢的主题。也有一些扩展，删除可能会有问题，或者它们相当大，如 Live Share。

你不会真的想确保它们出现在每一个个人资料中，所以我添加了一个名为`profileSwitcher.extensionsIgnore`的设置。这是一个扩展 ID 的数组，你想在每个配置文件，默认情况下，我将这些设置为被忽略:

```
shan.code-settings-sync
ms-vsliveshare.vsliveshare
ms-vsliveshare.vsliveshare-audio
ms-vsliveshare.vsliveshare-pack 
```

Enter fullscreen mode Exit fullscreen mode

如果你想添加更多，你需要编辑你的`settings.json`文件并添加设置。如果你手动设置，你需要包括上面的 4 个(假设你想忽略它们)，因为这是默认设置，直到手动设置。

## 升级注意事项

在升级到最新版本时，有一点需要注意，如果你在到`0.3.0`之前保存了一个配置文件*，它将没有与之相关的扩展，所以你需要重新保存这个配置文件，否则当你切换时，它会认为你在配置文件中没有任何相关的扩展，并删除你所有的扩展(见[错误#6](https://github.com/aaronpowell/vscode-profile-switcher/issues/6#issuecomment-513090629) )。*

## 总结起来

我真的很高兴这个功能登陆，因为当我第一次发布它时，几乎每个人都问我这个问题！这有点棘手，VS 代码并没有真正提供一个 API 来用扩展做这种事情(不能怪他们，你不想让扩展容易操纵其他扩展！)但现在在`0.3.3`版本中似乎运行良好。

如果你正在使用这个扩展，我很想听听它，知道它对你有什么帮助，以及你希望在未来看到什么样的特性。