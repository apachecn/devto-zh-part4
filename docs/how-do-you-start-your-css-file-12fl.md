# 你如何开始你的 css 文件？

> 原文：<https://dev.to/the24thds/how-do-you-start-your-css-file-12fl>

我一般是这样开始的:

```
*:not(ul):not(ol) {
  margin: 0;
  padding: 0;
} 
```

它恢复了我在开始保留列表标记中关心的大多数浏览器特定的样式。