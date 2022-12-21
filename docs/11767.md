# ECMASCRIPT:无效合并

> 原文：<https://dev.to/numtostr/ecmascript-nullish-coalescing-4mdl>

零化合并处于第三阶段。这个很有意思。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [ tc39 ](https://github.com/tc39) / [提议-取消-合并](https://github.com/tc39/proposal-nullish-coalescing)

### 无效合并提案 x？？y

<article class="markdown-body entry-content container-lg" itemprop="text">

# 看好 javascript 的合并

## 状态

当前阶段:

*   第四阶段

## 作者

*   加布里埃尔·伊森伯格( [github](https://github.com/gisenberg) ， [twitter](https://twitter.com/the_gisenberg) )
*   丹尼尔·埃伦贝尔( [github](https://github.com/littledan) ， [twitter](https://twitter.com/littledan) )
*   丹尼尔·罗森瓦塞尔( [github](https://github.com/DanielRosenwasser) ， [twitter](https://twitter.com/drosenwasser) )

## 概述和动机

当执行属性访问时，如果该属性访问的结果是`null`或`undefined`，通常希望提供默认值。目前，在 JavaScript 中表达这种意图的典型方式是使用`||`操作符。

```
const response = {
  settings: {
    nullValue: null,
    height: 400,
    animationDuration: 0,
    headerText: '',
    showSplashScreen: false
  }
};
const undefinedValue = response.settings.undefinedValue || 'some other default'; // result: 'some other default'
const nullValue = response.settings.nullValue || 'some other default'; // result: 'some other default'
```

这对于常见的`null` …

</article>

[View on GitHub](https://github.com/tc39/proposal-nullish-coalescing)