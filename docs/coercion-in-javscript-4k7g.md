# JavaScript 中的强制

> 原文：<https://dev.to/godcrampy/coercion-in-javscript-4k7g>

## 什么是强制？

强制是将一种数据类型转换为另一种数据类型的过程。

```
1 + '2'
// 1 coerces to '1'
// '12'

1 + true
// true coerces to 1
// 2

1 + null
// null coerces to 0
// 1

1 + undefined
// undefined coerces to 0
// 1

'abc' + undefined
// undefined coerces to 'undefined'
// 'abcundefined'

1 < 2 < 3
// => true < 3 (left to right associativity)
// => 1 < 3 (coercion)
// => true

1 > 2 > 3
// => false > 3
// => 1 > 3
// => false 
```

Enter fullscreen mode Exit fullscreen mode

JavaScript 在比较时可能会给出奇怪和意想不到的结果。因此，最好使用`===`而不是`==`进行比较，因为`===`并不强制。

```
1 == '1'
// true
// coercion

1 === '1'
// false 
```

Enter fullscreen mode Exit fullscreen mode

> 强制决策由 JavaScript 引擎本身做出。

## 手动强制

下面是如何手动转换成数据类型:

```
const number = 42;
const string = '42';

Number(string);
// Coerces to number
// 42

String(number);
// Coerces to string
// '42'

Boolean(string);
// Coerces to boolean
// 1 
```

Enter fullscreen mode Exit fullscreen mode

## 条件语句

*内的条件有*、*三进制*、 *while* 循环等。被强制转换为布尔型。

#### 真真假假

任何强制*为真*的值被称为*真*而强制*假*的值被称为假。

只有 6 个*假值*，其他都是*真值*:

1.  *假*
2.  *0*
3.  *、*、*、*(空字符串)
4.  *null*
5.  *未定义*
6.  *楠*

#### 当心这些:

```
Boolean({});
// true

Boolean([]);
//true

Boolean("0");
// true

null == 0
// false 
```

Enter fullscreen mode Exit fullscreen mode

> 这就是所有的人！谢谢你的阅读，祝你有美好的一天😄