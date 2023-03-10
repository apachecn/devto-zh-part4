# 在 JavaScript 中对数字对象使用 toLocaleString

> 原文：<https://dev.to/orimdominic/using-tolocalestring-for-number-objects-in-javascript-1bfh>

JavaScript 中的`toLocaleString`方法存在于数组、日期、数字对象和对象数据类型中。本文主要关注 JavaScript number 对象上的`toLocaleString`方法。注意，在撰写本文时,`toLocaleString`在 Node.js 中并不存在。

当在 JavaScript number 对象(不是像 234 或 3 这样的数字文字，而是一个标识符——一个变量或常数——带有一个数字值)上调用`toLocaleString`方法时，它根据传递给它的值对数字进行格式化，并返回格式化值的字符串表示。那么，如果这就是`toLocaleString`方法所能做的，为什么不使用`toString`？好问题！

`toLocaleString`方法接受参数，告诉它将数字对象格式化成哪种人类语言。换句话说，英国人会把“五十万”写成`500,000`，而西班牙人会写成`500.000`。`toLocaleString`将接受数字`500000`，如果你让它为英国人格式化，它将返回`'500,000'`。如果要求为西班牙人格式化它，它将返回`'500.000'`。每个人都会用自己的母语理解这个数字。谈论巴别塔，但对人类的 JavaScript 数字。它也支持阿拉伯语。这不是很棒吗？

好吧，空谈是廉价的。给我看看代码。

```
let num = 500000;
let britishNum = num.toLocaleString("en-GB"); //en-GB: Great Britain English
let numeroEsp = num.toLocaleString("es-ES"); //es-ES: Spanish Spanish
let arabicNum = num.toLocaleString("ar-EG"); //ar-EG: Eastern Arabic
console.log("British representation: ", britishNum); // 500,000
console.log("Spanish representation: ", numeroEsp); // 500.000
console.log("Arabic representation: ", arabicNum); // ٥٠٠٬٠٠٠ 
```

Enter fullscreen mode Exit fullscreen mode

棒极了。下面是一些解释，以及更多令人敬畏的事情。

`toLocaleString`实现的格式或语法是这样的:`numObj.toLocaleString([locales [, options]])`，其中

*   `numObj`是要格式化的数字对象，
*   `locales`代表一个字符串(如`es-ES`或`ar-EG`)，它是要格式化的语言，(参见[这个堆栈流问题](https://stackoverflow.com/questions/3191664/list-of-all-locales-and-their-short-codes)中的`locales`代码列表)。)
*   是一个对象，它的属性包含一些非常棒的格式化选项，比如货币的格式化，你想要多少位小数。方括号`[]`只表示`locales`和`options`是可选的。换句话说，你可以使用`toLocaleString`方法而不用传递它们。

😲那么如果没有传入任何参数，它会被格式化成什么语言呢？当然是你浏览器的默认语言！。

现在到了真正令人惊叹的部分。你说空谈是廉价的？给我看看代码。

*   货币格式

```
let num = 500000;
let britishCurrency = num.toLocaleString("en-GB", {
  style: "currency",
  currency: "GBP",
});
let japaneseCurrency = num.toLocaleString("ja-JP", {
  style: "currency",
  currency: "JPY",
});

console.log("British currency: ", britishCurrency); // £500,000.00
console.log("Japanese currency: ", japaneseCurrency); // ￥500,000 
```

Enter fullscreen mode Exit fullscreen mode

*   小数位数

```
let num = 500000.5525;
let british = num.toLocaleString("en-GB", {
  maximumFractionDigits: 3,
});
let spanish = num.toLocaleString("es-ES", {
  maximumFractionDigits: 2,
});

console.log("British: ", british); // 500,000.553
console.log("Spanish: ", spanish); // 500.000,55 
```

Enter fullscreen mode Exit fullscreen mode

显示代码。现在开始解释，好吗？

在`locales`参数之后传入的对象包含一些属性，用户可以随意定制这些属性以获得他们想要的数字格式。`style`
`currency`和
`maximumFractionDigits`就是其中的一些属性。持有这些属性的对象是上面语法中的`options`参数。

您可以通过使用`options`对象定制您的`toLocaleString`输出来查看 [MDN 文档。](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString#Using_options)

祝你在数字对象上使用`toLocaleString`好运！

封面图片由[网站打开](https://www.sitepen.com/)