# 单击后下拉菜单不关闭

> 原文：<https://dev.to/smilerathor/menu-drop-down-doesn-t-close-after-click-58ka>

大家好，

我刚刚开始学习 Angular，并创建了一个引导菜单栏。但是每当我点击有下拉菜单的菜单栏时，它不会在我悬停到下一个元素后关闭，它只会在我点击其他地方时关闭。
但是我想在悬停时关闭它，
我找到一个代码
。下拉菜单>。下拉开关:激活{
/ *没有这个，点击会变粘* /

```
 pointer-events: none; 
```

}

但是使用这段代码，主 Div 的 click even 不起作用。我进行了深入的搜索，但一无所获，所以我把它贴在这里以寻求帮助。

谢谢