# 伪造的 SVG 路径笔画模式

> 原文：<https://dev.to/netsi1964/faked-svg-path-stroke-pattern-2dkl>

SVG 形状的笔画只能有一个恒定形状。虽然 Affinity Designer 和 Adobe Illustrator 等矢量绘图应用程序提供了高级的笔画模式，但它尚未登陆 SVG。这支小笔用了一种很重又不太重的方法来生成看起来像图案的东西，但肯定不是。它结合使用添加路径的许多副本和更改 stroke-dasharray 来仅绘制一个 1 像素宽的路径，该路径在给定点具有计算的笔画宽度。有趣，可以玩，但不能用于现实世界。

[https://codepen.io/netsi1964/embed/yLBMYPo?height=600&default-tab=result&embed-version=2](https://codepen.io/netsi1964/embed/yLBMYPo?height=600&default-tab=result&embed-version=2)