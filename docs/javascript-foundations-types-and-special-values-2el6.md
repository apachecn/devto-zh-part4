# Javascript 基础:类型和特殊值

> 原文：<https://dev.to/juanelojga/javascript-foundations-types-and-special-values-2el6>

我在很多文章和书籍中读过“在 Javascript 中一切都是对象”。这是不正确的。

## 为什么？

Javascript 是一种动态语言，但这并不意味着它没有类型。官方的 Javascript 规范说([http://www . ECMA-international . org/ECMA-262/10.0/index . html # sec-ecmascript-overview):](http://www.ecma-international.org/ecma-262/10.0/index.html#sec-ecmascript-overview):)

> 原始值是以下内置类型之一的成员:未定义、Null、Boolean、Number、String 和 Symbol。对象是内置类型对象的成员；函数是一个可调用的对象。通过属性与对象关联的函数称为方法。

意味着“3”不等于 3，因为第一个是数字，第二个是字符串。这两个值有不同的类型。

类型的定义是:类型是一组内在的内置特征，唯一地标识特定值的行为，并将其与其他值区分开来，告诉编译器或解释器程序员打算如何使用数据。

Javascript 中的类型与强类型语言中的类型略有不同，在强类型语言中,“类型”这个词有更多的含义，但这足以证明说 Javascript 中的一切都是对象是不正确的。

## 类型

Javascript 定义了七种(将来是八种)内置类型:

*   空
*   不明确的
*   布尔型
*   数字
*   线
*   目标
*   符号(es6)
*   比金斯(顺)

除了 object 之外，所有这些类型都称为原语。

重要的是要说:**在 JavaScript 中，变量没有类型，值有**。

*typeof* 运算符检查给定值的类型，并返回一个字符串，其中包含前面列出的字符串值之一。

```
typeof undefined     === "undefined"; // true
typeof false         === "boolean";   // true
typeof 3             === "number";    // true
typeof "3"           === "string";    // true
typeof { track: 3 }  === "object";    // true
typeof Symbol()      === "symbol";    // true 
```

存在 null 的特殊情况。

```
typeof null === "object"; // true 
```

在 JavaScript 的第一个实现中，JavaScript 值被表示为一个类型标记和一个值。对象的类型标记为 0。null 表示为空指针(在大多数平台中为 0x00)。因此，null 有 0 作为类型标记，因此返回值的“对象”类型。

有人提议对 ECMAScript 进行修复，但遭到拒绝。

### 未定义，未声明

未赋值的变量属于未定义类型。

```
var a;
typeof a; // "undefined" 
```

一个*未定义的*变量是一个已经在可访问范围内声明的变量，但是目前在其中没有值。未声明的*变量是指没有在可访问范围内声明的变量。但是*操作符的*类型返回*未定义的*，即使对于*未定义的*或*未声明的*变量。* 

```
var a;
typeof a; // "undefined"
typeof b; // "undefined" 
```

*未定义的*和*空值*通常被认为是可互换的，要么是*空值*要么是*空值*。但是 *null* 是一个特殊的关键字，不是一个标识符，因此，你不能把它当作一个变量来赋值。然而“未定义”是一个标识符。

*   *null* 是一个空值，或者曾经有一个值，现在没有了。
*   *未定义*是缺失值或还没有值。

### 【未初始化】(TDZ)

在 ECMAScript 6 中，在声明 let 或 const 变量之前(在其作用域内)访问该变量会导致 ReferenceError。

TDZ o 时间死区捕捉编程错误非常有用，因为能够在声明变量之前访问它是很奇怪的。如果你这样做，通常是意外，你应该得到警告。

```
typeof a; // ReferenceError
typeof b; // "undefined"
let a; 
```

如果您的代码在到达“let a”语句之前试图以任何方式访问“a”，程序将抛出“ReferenceError”。在定义之前声明一个引用“a”的方法是可以的，只要该方法在 TDZ 中没有被执行，并且只要没有到达 let“a”语句,“a”就将在 TDZ 中(当已经进入作用域时)。这段代码不会抛出，因为 return there 直到离开 TDZ 后才会执行。

## 值

数组、字符串和数字是任何程序最基本的组成部分，但是 JavaScript 在这些类型上有一些独特的特征，可能会让你高兴，也可能让你困惑。在本文中，我将描述特殊值。每个 JS 开发人员都需要了解并正确使用分布在各种类型中的一些特殊值。

### NaN:无效数字

在两个操作数都不是数字的情况下执行的任何数学运算都将导致运算无法产生有效的数字，在这种情况下，您将获得 NaN 值。

```
var a = 2 / "a";      // NaN
typeof a === "number" // true 
```

NaN 是一种“值”,它表示数集中一种特殊的错误情况。错误条件是:“我试图执行一个数学运算，但失败了，所以这里是失败的数字结果。”

名为“is NaN()”的内置全局实用程序告诉我们该值是否为 NaN，但是“isNaN()”实用程序有一个致命的缺陷。它似乎试图从字面上理解“南”的意思。它的工作基本上是:“测试传入的东西要么不是一个数字，要么是一个数字。”但这不太准确。

```
var a = 2 / "foo";
var b = "foo";

a; // NaN
b; // "foo"

isNaN( a ); // true
isNaN( b ); // true - wtf??? 
```

显然，“foo”不是一个数字，但它也不是 NaN 值！

从 ES6 开始，最终提供了一个替代实用程序:

```
var a = 2 / "foo";
var b = "foo";

Number.isNaN( a ); // true
Number.isNaN( b ); // false 
```

南有一个特殊的事实:**南不等于它自己。NaN 是整个语言中唯一正确的值。每隔一个值总是等于自身**。

### 不定式

来自 C 等传统编译语言的开发人员可能习惯于看到编译器错误或运行时异常，如“被零除”，然而，在 Javascript 中，这种操作是明确定义的，并导致值无穷大。

```
var a = 1 / 0;  // Infinity
var b = -1 / 0; // -Infinity 
```

然而，一旦你溢出到其中一个无限，就没有回头路了。换句话说，你可以从有限到无限，但不能从无限回到有限。

### 负零

JavaScript 既有正常的零 0(正的零+0)，也有负的零 0。

```
var a = 0 / -3; // -0
var b = 0 * -3; // -0 
```

加法和减法不能产生负零。

```
var a = 0;
var b = 0 / -3;

a == b;     // true
a === b;    // true - ??

0 > -0;     // false

a > b;      // false

Object.is(b, -0) // true
Object.is(b, 0)  // false 
```

在某些应用程序中，开发人员使用一个值的大小来表示一条信息(如每个动画帧的移动速度)，并使用该数字的符号来表示另一条信息(如移动的方向)。在这些应用中，如果一个变量到达零，它失去了它的符号，那么你将失去它在到达零之前的运动方向的信息。保留零的符号可以防止潜在的不必要的信息丢失。

## 总结

JavaScript 有七种(即将有八种)内置类型:null、undefined、boolean、number、string、object、symbol、bigint (future)。它们可以通过运算符的类型来识别。

变量没有类型，但是变量中的值有。

*未定义*是一个已声明变量可以保存的值，*未声明*意味着一个变量从未被声明过。空类型只有一个值:null，同样，未定义的类型也只有未定义的值。如果没有其他值，undefined 基本上是任何变量或属性的默认值。