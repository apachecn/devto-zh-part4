# Firefox 68.0 获得了颜色对比辅助功能检查器

> 原文：<https://dev.to/drewtownchi/firefox-68-0-gets-a-color-contrast-accessibility-checker-4dlg>

Chrome via Lighthouse 中的审计已经进入了最新版本的 Firefox。该功能仍处于测试阶段，但提供了一种简单的方法来快速检查页面中不符合 WCAG 颜色对比标准的颜色配对。

颜色对比是可访问性的重要组成部分，因为它使文本更容易被每个人阅读，而不仅仅是那些有障碍的人。

要运行审计，打开开发者工具(F12) ->点击开发者工具菜单中的`Accessibility`->点击`Turn On Accessibility Features`，如有必要，最后点击“检查问题”下顶部子菜单中的`contrast`。

审核完成后，您可以看到哪些文本不符合标准，以及有关原因的详细信息，如使用的颜色和这些颜色的比例。

测试愉快！