# 使用触摸条切换黑暗模式

> 原文：<https://dev.to/oihamza/toggle-dark-mode-with-touch-bar-3o8n>

如果你和我一样，你喜欢根据你的感受在黑暗模式和光明模式之间转换。

这里有一个快速而中肯的教程，我将向你展示如何在你的 MacBook 的 Touch Bar 上添加一个快捷方式，让切换变得更加容易。

🌞🌚

[![cat saying are you ready](img/d56ffbeb8112c080452e661910c30230.png)](https://i.giphy.com/media/CjmvTCZf2U3p09Cn0h/giphy.gif)

# 我们将要做什么

非常简单，我们将创建一个 Automator 脚本，允许我们创建暗/亮模式的快捷方式。一旦我们设置好脚本，我们将添加一个 Touch Bar 快捷方式，使两种模式之间的切换更加容易。

如果你不熟悉这些东西，不要担心，因为我已经列出了每个步骤，并提供了有用的截图来指导你。我们有这个！

# 你需要什么:

*   带触控条的 MacBook👆🏽💻
*   马科斯[莫哈韦](https://www.apple.com/macos/mojave/)

## 步骤 1)

<sub><sup>创建自动化光线→黑暗模式过程的脚本</sup></sub>

→打开 Automater
→创建新文档
→创建快速动作，然后点击“选择”

[![Create a new document and then quick action](img/cb1c1083da33f024dedba18dd7b289a8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--c9ddTTri--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/y9tfj26coyaci5erppof.png)

## 第二步)

<sub><sup>执行脚本</sup></sub>

将工作流程选择更改为“无输入”

[![change to no input](img/06fa41f109950a22811c91ea138dbf02.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CXpaFl6V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/e8gkxmb7dqy6ulyqlb4g.png)

将图像更改为您想要的图标。这将出现在触摸栏上。我选择了色轮。

[![change the image](img/84a1ca391587c2552fca00007e2be05b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zv3F_dfc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dd3j2n5smx7ifq16yfl2.png)

在操作搜索栏中键入“运行 AppleScript”

[![Alt Text](img/46bcb692ccbf54176b364a8e452d6c5b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xbx1_BTT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kmtyn55qm364sk0xfmqq.png)

双击“运行 AppleScript ”,您会看到这个页面

[![Run AppleScript page](img/2548dae738f596c680aaa279c0b5a59a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8oYE2Prw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/db0t33krwu2gqq9k08q9.png)

删除现有代码，替换为以下代码:

`tell application "System Events"
tell appearance preferences
set dark mode to not dark mode
end tell
end tell`

它应该是这样的:

[![Updated code](img/3e379859737f0e17d68ae09a4ee7085f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kFI8IEX4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/p6z03n4vr7r0fej38sa6.png)

让我们通过点击“运行”按钮来测试这个脚本

[![Test the script](img/00cf056f5be8d2f9a20cba3d2fb5af1f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UY3a2Me9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9uo887xwl50lzohdn64b.png)

再次点击“运行”切换回轻模式。

让我们继续保存脚本。我把我的命名为“黑暗模式切换”

[![Save your script](img/b9287e0cc331681e3c811f8a16a24a67.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5LFYVJ_i--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/utqekyfsjy09vfh2wka2.png)

## 第三步)

<sub><sup>创建触摸栏快捷方式。我们快到了！</sup>T3】</sub>

在**系统偏好** → **扩展**上，确保选中黑暗模式切换

[![system preferences → extensions](img/ad86843af4a6cc514245c19a4388bdd9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nFg0kYd1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t1b28k86wszx9o53ircs.png)

点击**定制控制条**

[![customize control strip](img/e1aeaf8b634cab3cff9d5fdd4cd29fb8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4MKo-w28--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o01a3c1fh339ni7ngcm0.png)

这将把你带到这个页面，在这里你可以把你的快速动作拖动到键盘上你想要的位置。我选择把我的放在最右边。

注意:你必须在你的触摸栏上把它拖向你想要的地方。[查看更多此处](https://support.apple.com/guide/mac-help/touch-bar-mchlbfd5b039/mac)。

[![Touch Bar icons](img/64ae620179230c05d5d487c39c735dcf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iBao6rtu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yksx618ds4n3imfxkaec.png)

[![Drag the icon to your desired Touch Bar location](img/2fe30898369cee57bebd8b8fe0232a4e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ztqcSl95--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dxjwra4svarpowwzb9xc.jpeg)

恭喜你，你做到了！😎

在 Twitter 上与我联系 [@oiHamza](https://twitter.com/oihamza) 。👨🏽‍🚀