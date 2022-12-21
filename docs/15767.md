# iTerm2 快捷键

> 原文：<https://dev.to/changoman/iterm2-shortcut-keys-38cc>

iTerm2 是开发人员和程序员最喜欢的终端之一。尽管默认快捷键很棒，但还是有些欠缺。您可以在您的个人资料下添加自己的快捷键，让 iTerm2 做一些令人惊叹的事情。

[![](img/c297e80ab4d96aea8277eb24b2f37034.png)](https://i.giphy.com/media/MXd4zQQvDsg93ZMoms/giphy.gif)

**首先，进入 iTerm2 >偏好设置>配置文件>按键**

在这里，您可以看到当前的键映射。单击“+”按钮添加快捷方式组合。您需要为某些操作输入正确的十六进制代码。这是一个视频:

[![](img/50f4845268a58a434e038365abe22868.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GhVe7oRl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://content.screencast.com/users/hunter1291/folders/Jing/media/712134d0-2383-49ab-be3d-c984fe3f7054/00000003.png)

以下是我为自己绘制的按键:

**跳到行首和行尾:**

[![](img/f7d7894a185569ed8db6b0fb336bc399.png)](https://i.giphy.com/media/XAyUMosNkO4tOx9suX/giphy.gif)T3】

```
// Skip to Beginning of Line
command + left-arrow
Send Hex Code
0x01 
```

Enter fullscreen mode Exit fullscreen mode

```
// Skip to End of Line
command + right-arrow
Send Hex Code
0x05 
```

Enter fullscreen mode Exit fullscreen mode

**跳到单词的左右:**

[![](img/cdcb3e48381de63edaa35b4700dd0c2b.png)](https://i.giphy.com/media/XE0mreHUkRtBYK2slw/giphy.gif)T3】

```
// Skip Left of Word
option + left-arrow
Send Escape Sequence
b 
```

Enter fullscreen mode Exit fullscreen mode

```
// Skip Right of Word
option + right-arrow
Send Escape Sequence
f 
```

Enter fullscreen mode Exit fullscreen mode

**删除单个单词:**

[![](img/c297e80ab4d96aea8277eb24b2f37034.png)](https://i.giphy.com/media/MXd4zQQvDsg93ZMoms/giphy.gif)T3】

```
// Delete Single Word
option + delete (The shortcut combo will look like ⌥←Delete)
Send Hex Code
0x17 
```

Enter fullscreen mode Exit fullscreen mode

**删除整行:**

[![](img/b6a0097ea0f9961d80d902bd11fa7369.png)](https://i.giphy.com/media/Lr3XZTmAKbuBIdLiAG/giphy.gif)T3】

```
// Delete Entire Line
command + delete (The shortcut combo will look like ⌘←Delete)
Send Hex Code
0x15 
```

Enter fullscreen mode Exit fullscreen mode

希望这些快捷键对你有用！想了解更多关于 web 开发的技巧和诀窍，请访问我在 https://codebushi.com 的博客