# 函数、过程、方法、运算符...

> 原文：<https://dev.to/stereobooster/function-procedure-method-operator-2dpf>

有太多的术语表示相同或几乎相同的意思。让我们弄清楚什么是什么。

函数、lambda、procedure、routine(子程序)、program(子程序)、application(作为名词)、算法、方法、闭包、operator(操作)有什么共同点？所有这些都是计算。

> 有几种方法来定义计算。现在，我举一个最简单的例子:**计算是一系列的步骤，我称之为行为**。一个步骤有三种常见的选择，导致**三种不同的行为**:
> 
> *   行动行为...
> *   国家行为...
> *   国家行为...
> 
> - [计算和状态机](https://lamport.azurewebsites.net/pubs/state-machine.pdf)。莱斯利·兰波特，2008 年 4 月 19 日

## 功能 vs 程序

函数有一个数学定义(我在这里写了):

*   接受至少一个输入
*   产生一个输出
*   因为同样的输入总是产生同样的输出
*   没有任何副作用-例如，执行函数的唯一结果是它的输出，其他什么都没有发生

从数学 PoV 来看，函数与 lambda 相同(可表示为希腊字母λ)。

但并不是所有的编程语言都遵循严格的定义(我猜是历史原因)。例如，JavaScript:

相同的输入但不同的结果:

```
let counter = 0;
const increase = (x) => counter += x;
increase(1) !== increase(1) 
```

函数的结果不与返回值通信，而是与副作用通信:

```
console.log('Hello, world!');
// Outputs Hello, World
// Returns undefined 
```

没有输入值:

```
Date.now(); 
```

这不是一个严格的规则，因为我们可以把它想成:

```
Date.now(undefined); 
```

一些编程语言试图区分函数和“非函数”。比如[帕斯卡](https://wiki.freepascal.org/):

> 过程是不返回值的例程。与过程相反，函数是一个返回一个值的例程。
> 例程是执行某些功能的可重用源代码。

问题是 Pascal 没有限制函数内部副作用的使用，所以除法不精确。

有些编程语言根本没有“无函数”，比如 Haskell。那他们是如何处理副作用的呢？他们使用单子来建模 IO(另一篇文章的主题)。

## 例程、程序、应用程序

来自[帕斯卡](https://wiki.freepascal.org/)的程序定义:

> 例程是执行某些功能的可重用源代码。

从[开始，C 编程语言](https://www.dipmat.univpm.it/~demeio/public/the_c_programming_language_2.pdf):

> 在 C 中，函数相当于 Fortran 中的子程序或函数，或者 Pascal 中的
> 过程或函数。函数提供了一种方便的方法来封装一些计算，然后可以使用它而不用担心它的实现。

所有这些都是相同的:例程(子例程)、程序(子程序)、应用程序(作为名词)——一些可重用的计算。

例如，在 Bash 中，您可以将单独的程序(或应用程序、或二进制文件、或可执行文件)视为“函数”——例如，从一个程序获取输出，并将其传递(通过管道)到另一个程序。

```
cat /var/log/syslog | grep 'post' 
```

`cat` -连接文件并在标准输出上打印。
`grep` -打印与图案相匹配的线条。

## 算法

> 算法-解决问题或完成某些目标的一步一步的过程
> 
> ——[韦氏词典](https://www.merriam-webster.com/dictionary/algorithm)

广义来说，算法等同于计算。有时，人们把算法称为预制的解决方案(与设计模式一样):

> 算法——适用于计算机实现的解决问题的方法。算法与数据结构——组织数据的方案——密切相关
> 
> ——[《算法》，第 4 版，罗伯特·塞奇威克和凯文·韦恩。答](https://algs4.cs.princeton.edu/10fundamentals/)

比较对象:

> 设计模式是由三部分组成的规则，它表达了特定上下文、问题和解决方案之间的关系。简而言之，这个模式同时也是一个东西，...，以及告诉我们如何创造那个东西，以及何时必须创造它的规则。
> 
> -一种模式语言:城镇、建筑、施工，1977 年

## 闭包和方法

闭包是一个附加了环境的计算(函数)。例如，在 JavaScript 中:

```
const True = (x) => (y) => x;
const condition = True('then');
condition('else') === 'then'; 
```

`condition`是功能，附带环境。环境 enclosing`condition`包含`x`所以它工作，但是在这个闭包之外没有`x`:

```
const NotTrue = (z) => (y) => x;
const condition = NotTrue('then');
condition('else'); // Uncaught ReferenceError: x is not defined 
```

该方法是一个附加了对象的计算(函数)。通常通过`this`或`self`关键字可以访问附加的对象。例如，在 JavaScript 中:

```
class DogClass {
  constructor(name) {
    this.name = name;
  }
  bark() {
    return `${this.name} is a good dog!`;
  }
}
const belka = new DogClass('Belka');
belka.bark(); 
```

`bark`是方法。

在一些 PL 中，对象的传递是显式的，例如在 [Lua](https://www.lua.org/pil/16.html) :
中

```
function Account.withdraw (self, v)
  self.balance = self.balance - v
end 
```

## 运算符

在数学方面:

> 运算符是表示数学运算的符号或函数。
> 
> - [WolframAlpha](https://www.wolframalpha.com/input/?i=operator)

在数学中，运算符可以被视为函数的特殊版本，通常运算符有两个参数并使用中缀符号(例如，它被放在符号之间)。比如中缀符号:

```
1 + 1 
```

前缀符号:

```
f(a, b) = a + b
f(1, 1) 
```

没有人使用这个符号，但它是一样的:`+(1, 1)`。

在 PL 中，运算符可以有不同于函数的处理方式。比如在 JavaScript 中，有[条件运算符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Expressions_and_Operators#Conditional_(ternary)_operator)。

```
age = 20
(age >= 18) ? console.log('adult') : console.log('minor');
// outputs adult
// returns undefined 
```

如果我们想实现一个函数:

```
const ternary = (condition, thenAction, elseAction) => {
  if (condition) {
    return thenAction;
  } else {
    return elseAction;
  }
}
ternary(age >= 18, console.log('adult'), console.log('minor'));
// outputs adult
// outputs minor
// returns undefined 
```

这里的问题是`console.log('adult')`、`console.log('minor')`是在传递给三元函数之前执行的。我们可以通过将动作包装成函数(`() => ...` ):
来解决这个问题

```
const ternary = (condition, thenAction, elseAction) => {
  if (condition) {
    return thenAction();
  } else {
    return elseAction();
  }
}
ternary(age >= 18, () => console.log('adult'), () => console.log('minor')); 
```

但是正如你所看到的，操作符和函数的处理是不同的(在 JS 中)。

另一方面，有些语言没有这个问题，例如，在 [Haskell](https://wiki.haskell.org/If-then-else) 中，将`if`实现为函数是可能的，不需要包装额外的函数。因为 Haskell 正在做懒惰的评估，也就是按需调用。

在一些语言中方法和一些操作符可以相同，例如，在 Ruby:

```
a + b 
```

与
相同

```
a.+(b) 
```

这是一个令人困惑的术语，因 PL 而异。

## 常规和闭包

从编译的角度来看，有时人们会区分例程(想想 C 函数)和闭包。因为例程可以实现为一个普通的指针，但是[闭包编译需要更多的工作](http://matt.might.net/articles/closure-conversion/)。

> 照片由马特·阿特兹在 Unsplash 上拍摄