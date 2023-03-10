# 点符号与括号符号

> 原文：<https://dev.to/samanthaming/dot-notation-vs-bracket-notation-1l6l>

[![Code Tidbit by SamanthaMing.com](img/42ba306aabf3f40a4fed23d7c00f41cf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qYEr_4Mv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cq4flon7wiummdvftw8v.png)

这两种表示法都可以访问对象属性。但问题往往是我应该使用哪一个🤔。不要再想了，跟着 Airbnb 的风格指南走就行了。始终使用点号。当你想用一个变量访问对象属性时，使用括号👍

```
// Dot notation vs Bracket notation

const variable = 'cookie';

const snack = {
  cookie: '🍪'
};

// ✅ Dot: access property
snack.cookie;

// ✅ Bracket: access property with variable
snack[variable]; 
```

## 访问对象属性

有两种方法可以访问对象属性。点和括号。

```
const obj = {
  name: 'value'
};

// Dot Notation
obj.name; // 'value'

// Bracket Notation
obj['name']; // 'value' 
```

## 为赢点符号

我记得我第一次学这个的时候。理解这两种不同的方式很简单。没什么太复杂的。但我关心的从来不是不同的符号。我最大的难题是，我应该使用哪个？？🤯

如果你像我一样！这是细目分类。他们都做同样的事。这是一个简单的规则。默认情况下，只使用点符号。

✅圆点符号🏆

*   读起来容易多了
*   打字快多了。

## 点符号的局限性

任何规则都有例外😂。所以让我们来看看一些限制。

a.使用标识符的问题 b .使用变量的问题

## a .使用 JavaScript 标识符

使用点符号的一个主要限制是它只适用于有效的标识符。首先，让我来定义什么是**标识符**

> 标识符是代码中标识变量、函数或属性的字符序列。

*[MDN 网络文档](https://developer.mozilla.org/en-US/docs/Glossary/Identifier)*

**标识符**有以下规则:

*   区分大小写
*   可以包含 Unicode 字母
*   `$`、`-`，都是允许的
*   数字(0-9)可以，但不能以数字开头

所以让我们来看一些例子，看看当我们使用点符号时会发生什么。

```
const obj = {
  123: 'digit',
  123name: 'start with digit',
  name123: 'does not start with digit',
  $name: '$ sign',
  name-123: 'hyphen',
  NAME: 'upper case',
  name: 'lower case'
}; 
```

*注:*

您可能会注意到，我不得不在一些属性名称中加入引号。例子:`123name`。我必须这样做，否则对象将被认为是无效的，它将抛出一个语法错误。

### 圆点符号

```
obj.123;      // ❌ SyntaxError
obj.123name;  // ❌ SyntaxError
obj.name123;  // ✅ 'does not start with digit'
obj.$name;    // ✅  '$ sign'

obj.name-123;  // ❌ SyntaxError
obj.'name-123';// ❌ SyntaxError

obj.NAME; // ✅ 'upper case'
obj.name; // ✅ 'lower case' 
```

看看我是如何在`obj.'name-123'`的例子中巧妙地使用引号的。好吧，不要，因为它仍然不会工作😂。

### 括号标注

但是对于括号符号来说，这些都不是问题。

```
obj['123'];     // ✅ 'digit'
obj['123name']; // ✅ 'start with digit'
obj['name123']; // ✅ 'does not start with digit'
obj['$name'];   // ✅ '$ sign'

obj['name-123']; // ✅ 'does not start with digit'

obj['NAME']; // ✅ 'upper case'
obj['name']; // ✅ 'lower case' 
```

### 裁决

如果您认为您有一个无效的 JavaScript 标识符作为您的属性键，请使用括号符号👍

## b .用变量访问属性

点符号的另一个限制是处理变量。你肯定应该使用括号符号。注意！当你在括号符号中引用一个变量时，你需要跳过引号。这就是为什么你知道你在处理一个变量，而不是访问属性键。

```
const variable = 'name';

const obj = {
  name: 'value'
};

// Bracket Notation
obj[variable]; // ✅ 'value'

// Dot Notation
obj.variable; // undefined 
```

### 未定义的属性

我想指出一件非常重要的事情。你会注意到，如果我尝试在变量上使用点符号，它会返回`undefined`。这是因为当你试图访问一个不存在的属性时，它会返回`undefined`。

```
const emptyObj = {};

obj.name; // undefined
obj['name']; // undefined 
```

所以，这是需要小心的部分。让我们回到前面的变量对象例子。如果您使用了点符号，它会假设您试图用有效的 JavaScript 标识符访问属性。因为它返回一些东西，你可能会认为一切都很好。引擎盖下，是的。但是如果你的意图是使用这个变量，它可能会让你迷惑。这肯定是调试头痛的一个来源。所以要小心！！

```
const variable = 'name';

const obj = {
  name: 'value',
  variable: '👻'
};

// Bracket Notation
obj[variable]; // ✅ 'value'

// Dot Notation
obj.variable; // '👻' 
```

### 裁决

使用变量时，切勿使用点符号

## 结论

知道了点符号的局限性，让我们更新我们的规则。

> 使用点符号。但是，如果您处理的是无效的标识符或变量，请使用括号符号。

* * *

## 社区输入

*   *[@ Marcello Nicoletti](https://dev.to/marcellonicoletti/comment/c3lh):*【点符号的另一个好处】它看起来也像其他类 C 语言中的对象用法。对于来自 C、C#、Java 的人来说，读写会更容易。

* * *

## 资源

*   [堆栈溢出:点符号与括号](https://stackoverflow.com/questions/4968406/javascript-property-access-dot-notation-vs-brackets)
*   [Airbnb 的 JavaScript 风格指南:属性](https://github.com/airbnb/javascript#properties)
*   [codeburst.io:点符号 vs 括号符号](https://codeburst.io/javascript-quickie-dot-notation-vs-bracket-notation-333641c0f781)
*   [MDN Web 文档:标识符](https://developer.mozilla.org/en-US/docs/Glossary/Identifier)
*   [属性点符号/括号符号](http://xahlee.info/js/javascript_dot_notation_vs_bracket_notation.html)

* * *

**感谢阅读❤**
问好！[insta gram](https://www.instagram.com/samanthaming/)|[Twitter](https://twitter.com/samantha_ming)|[脸书](https://www.facebook.com/hisamanthaming) | [媒体](https://medium.com/@samanthaming) | [博客](https://www.samanthaming.com/blog)