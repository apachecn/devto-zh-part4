# 通过 4 个简单的步骤让你的网站焕然一新

> 原文：<https://dev.to/misselliev/how-to-make-your-website-responsive-in-4-easy-steps-330l>

响应式网站意味着你的设计可以适应用户浏览网站的任何设备。

通过应用响应式设计原则，您可以确保您的网站能够在不同的屏幕尺寸上很好地工作，因此更多的人可以访问您的内容。考虑到你的大量用户很可能会通过他们的手机浏览你，考虑到这一点是很重要的。

以下是一些快速提示，您可以将它们应用到您的项目中，使其更具响应性:

### 使用媒体查询

媒体查询由媒体类型组成，如果该类型与显示文档的设备类型相匹配，则应用样式。我该怎么做？简单如下面这个语法:
`@media(max-min width:100px){
Your CSS rules go here}`

### 确保您的图像也获得响应备忘录

[![](img/e2ed4e02db50809e8e2051a5b2a16d13.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1xg1ViJn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4uk6iwyg7y7b0ddf38ap.jpeg) 
与其对一个元素应用绝对宽度(即宽度:70px)，不如用这样的:
`.img{
Max-width: 100%;
Display: block;
Height: auto;}`

在这里，最大宽度缩放以适应图像而不拉伸它，自动高度保持原始比例。

### 对于更高分辨率的显示器，使用视网膜图像

最简单的方法是将它们的宽度和高度定义为原来的一半。
`.img{
Height: 250px;
Width: 250px;}
<img src="linkpic500x500" alt="image">`

### 注意你的排版

不使用 px 或 em 单位，而是切换到视口单位。视口单位相对于设备的视口尺寸(宽度/高度),百分比相对于父容器元素的大小。
比如:
`.div{
Font-size: 10 vw; ->10% viewport width
3 vh; -> 3% viewport height;
Vmin: 70 vmin; -> 70% of viewport's smaller dimension
Vmax: 100 vmax; ->100% of viewport's bigger dimension`

应用这些技巧，你的网站在任何设备上都不会出现问题，**祝贺你**！