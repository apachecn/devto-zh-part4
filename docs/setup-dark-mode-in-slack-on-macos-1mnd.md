# 在 MacOS 上设置空闲时的黑暗模式

> 原文：<https://dev.to/nahuelhds/setup-dark-mode-in-slack-on-macos-1mnd>

### 2019 年 7 月更新😭

Slack 版本 4.0.0 与此解决方案不兼容。需要调查，如果我们可以在这个版本中这样做。

### 2019 年 6 月更新💁‍

*   在结尾添加了一个额外的步骤[(仅在我的博客上)](https://nahuelhds.dev/slack/2018/11/22/Setup-Dark-Mode-in-Slack-on-MacOS.html)，通过简单的双击自动实现黑暗主题。😁
*   移动应用的黑暗模式已经到来。在 Slack 博客上阅读。桌面黑暗模式仍在进行中。希望我们可以在这里使用这个小黑客。
*   更新了 rawgit.com 到 jsdelivr.com 的链接。

*学分为* [*本要旨*](https://gist.github.com/a7madgamal/c2ce04dde8520f426005e5ed28da8608) *。*

### 首先，配置通用主题👨‍💻

1.  关闭松弛
2.  打开这个文件`/Applications/Slack.app/Contents/Resources/app.asar.unpacked/src/static/ssb-interop.js`
3.  把这个附在最下面

```
document.addEventListener("DOMContentLoaded", function() {
  let tt__customCss =
    ".menu ul li a:not(.inline_menu_link) {color: #fff !important;}";
  $.ajax({
    url:
      "https://cdn.jsdelivr.net/gh/laCour/slack-night-mode@master/css/raw/black.css",
    success: function(css) {
      $("<style></style>")
        .appendTo("head")
        .html(css + tt__customCss);
    }
  });
}); 
```

### 其次，选择一个合适的侧边栏主题💅

1.  前往首选项/侧栏
2.  在该页的末尾，选择设置自定义颜色
3.  粘贴此自定义主题:

```
#171717,#404245,#424242,#ECF0F1,#4A4A4A,#FAFAFA,#2ECC71,#00A362 
```

在 [Slack 主题](https://slackthemes.net)可以看到其他侧边栏主题。我选的是[绿灯侠](https://slackthemes.net/#/green_lantern)。

### 享受黑暗😈

就是这样。您现在可以打开 Slack 并查看结果！

### 加成一步！自动化这个🤓

每次 Slack 做一点点更新，你都需要一次又一次地手动添加那个小脚本。

嗯，不再是了。[在我的博客上阅读](https://nahuelhds.dev/slack/2018/11/22/Setup-Dark-Mode-in-Slack-on-MacOS.html)