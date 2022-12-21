# 为网络优化图像的最快方法

> 原文：<https://dev.to/rrjoson/fastest-way-to-optimize-your-images-for-the-web-14cm>

有没有想过优化你的网页图片的最快方法是什么？

如果您不必为每种屏幕尺寸和屏幕密度手动创建优化的图像，会怎么样？如果您可以动态优化图像会怎么样？如果能提供像 WEBP 这样的高级图片格式呢？如果您可以只传递一个 URL 并获得一个优化的图像 URL 列表，那会怎么样呢？

如果你正在使用文件堆栈，那么检查一下[文件堆栈自适应](https://github.com/filestack/adaptive)。它允许你像这样传递一个未优化的图片 URL:

```
https://cdn.filestackcontent.com/5aYkEQJSQCmYShsoCnZN 
```

并获得一个可以在图片元素中使用的优化图片 URL 列表:

```
https://cdn.filestackcontent.com/resize=width:180/5aYkEQJSQCmYShsoCnZN 180w, 
https://cdn.filestackcontent.com/resize=width:360/5aYkEQJSQCmYShsoCnZN 360w, 
https://cdn.filestackcontent.com/resize=width:540/5aYkEQJSQCmYShsoCnZN 540w, 
https://cdn.filestackcontent.com/resize=width:720/5aYkEQJSQCmYShsoCnZN 720w, 
https://cdn.filestackcontent.com/resize=width:900/5aYkEQJSQCmYShsoCnZN 900w, 
https://cdn.filestackcontent.com/resize=width:1080/5aYkEQJSQCmYShsoCnZN 1080w, 
https://cdn.filestackcontent.com/resize=width:1296/5aYkEQJSQCmYShsoCnZN 1296w, 
https://cdn.filestackcontent.com/resize=width:1512/5aYkEQJSQCmYShsoCnZN 1512w, 
https://cdn.filestackcontent.com/resize=width:1728/5aYkEQJSQCmYShsoCnZN 1728w, 
https://cdn.filestackcontent.com/resize=width:1944/5aYkEQJSQCmYShsoCnZN 1944w, 
https://cdn.filestackcontent.com/resize=width:2160/5aYkEQJSQCmYShsoCnZN 2160w, 
https://cdn.filestackcontent.com/resize=width:2376/5aYkEQJSQCmYShsoCnZN 2376w, 
https://cdn.filestackcontent.com/resize=width:2592/5aYkEQJSQCmYShsoCnZN 2592w,
https://cdn.filestackcontent.com/resize=width:2808/5aYkEQJSQCmYShsoCnZN 2808w, 
https://cdn.filestackcontent.com/resize=width:3024/5aYkEQJSQCmYShsoCnZN 3024w" 
```

所以，下次你发现自己手动优化网页图像时，请查看文件堆栈自适应。