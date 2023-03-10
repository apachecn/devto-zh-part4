# 随机字符串生成器

> 原文：<https://dev.to/bugmagnet/random-strings-generator-482>

我正在将 ECMAScript 3 函数转换成 ES6 的“粗箭头”格式。它的确缩写了。

```
function randomString(c) {
  for (var a = [], b = 0; b < c; b++) {
    a.push(Math.floor(36 * Math.random()).toString(36));
  }
  return a.join("");
} 
```

Enter fullscreen mode Exit fullscreen mode

是它最初的定义。现在看起来是这样的:

```
const randomString = c => Array(c).fill(0).map(function (itm, idx) {
    return Math.floor(36 * Math.random()).toString(36);
  }).join(""); 
```

Enter fullscreen mode Exit fullscreen mode

ES3 版本比 ES6 版本稍快，但仅快 40 纳秒左右。`fill`和`map`可能有`for`没有的开销。

(几分钟后)

使 ES6 版本更加简洁:

```
const ES6randomString = c => Array(c)
  .fill(0)
  .map(() => Math.floor(36 * Math.random()).toString(36))
  .join(""); 
```

Enter fullscreen mode Exit fullscreen mode

但遗憾的是，ES3 版本仍然更快。有什么建议吗？