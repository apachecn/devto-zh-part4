# 什么是 TypeScript？

> 原文：<https://dev.to/travissanon/what-is-typescript-i42>

*摘自维基百科:*

> “TypeScript 是一种由微软开发和维护的开源编程语言。
> 
> 它是 JavaScript 的一个严格的语法超集，并为该语言添加了可选的静态类型。"

单词**“超集”**包含了原始版本的所有特征，但是被增强以包括额外的特征。

这意味着 TypeScript 基本上是带有一些额外特性(又名:静态类型)的 JavaScript，这些特性使 JavaScript 更易于使用。

> " TypeScript 是为开发大型应用程序和将编译转换成 JavaScript 而设计的."

之所以为大型应用程序设计 TypeScript，是因为 JavaScript 是一种松散类型的语言。

**“松散类型”**语言是一种不需要指定类型的语言。这基本上意味着你可以用你的表情做任何你想做的事情。

让我们看看这个例子。让我们使用 **JavaScript** 创建一个带有值为`"hey"`的字符串的问候变量。

```
/* JavaScript */
let greeting = "Hey" 
```

Enter fullscreen mode Exit fullscreen mode

我们希望类型仅仅是一个字符串，即使值改变了。JavaScript 知道值是一个字符串，但它不在乎。

```
/* JavaScript */
let greeting = "Hey" // ¯\_(ツ)_/¯ 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们把这个值改成一个数字。JavaScript 知道问候值变成了一个数字，但同样，它不在乎。)

```
/* JavaScript */
let greeting = "Hey" // ¯\_(ツ)_/¯

greeting = 1 // ¯\_(ツ)_/¯ 
```

Enter fullscreen mode Exit fullscreen mode

这基本上意味着 JavaScript 不关心返回什么。这很糟糕，因为您可能会无意中更改某个东西的值类型，破坏您的代码，而 JavaScript 只会让它自行其是。

现在让我们使用 **TypeScript** 来创建一个名为 greeting 的变量，并赋予它一个类型`string`，并使值成为相同的字符串。

```
/* TypeScript */
let greeting: string = "Hey" 
```

Enter fullscreen mode Exit fullscreen mode

因为我们设置了一个类型，这个变量的值将保持这个类型。如果你不这样做，编译器会对你大吼大叫。

```
/* TypeScript */
let greeting: string = "Hey" // This is a string. 
```

Enter fullscreen mode Exit fullscreen mode

假设我们试图将这个值更改为一个数字，TypeScript 将会抛出一个错误，并指出这是非法的。

```
/* TypeScript */
let greeting: string = "Hey" // This is a string.

greeting = 1 // Hey, thats illegal. 
```

Enter fullscreen mode Exit fullscreen mode

这就是 TypeScript 非常有用的原因，它可以防止随着应用程序变大而导致的错误。

#### 总结:TypeScript 是一种强类型 JavaScript

(您也可以在 TypeScript 中省略分号)

## 为什么

那么为什么要使用 TypeScript 呢？

*   为 JavaScript 提供可选的类型系统。如果您不想在应用程序变大时被运行时错误困扰，那么拥有一种带类型系统的语言是非常好的
*   在进行重构时增加你的灵活性。每当您进行更改时，TypeScript 编译器将捕获整个代码库中的错误。
*   类型是文档。通过用类型注释代码，它将始终与您的代码行为保持同步。所以现在你不必注释你的代码或者通读 READMEs(或者维护它们)。

## 如何

**安装打字稿:**

```
> npm install -g typescript 
```

Enter fullscreen mode Exit fullscreen mode

让我们写一些基本的 JavaScript。

```
/* index.ts */

let sentence = "Proud cat owner"

function welcomePerson(title) {
  return "Welcome, " + title
}

console.log(welcomePerson(sentence)) // Welcome, Proud cat owner 
```

Enter fullscreen mode Exit fullscreen mode

**编译打字稿:**

```
> tsc index.ts 
```

Enter fullscreen mode Exit fullscreen mode

编译后的代码看起来与`.ts`文件一样，但这只是为了展示您可以毫无问题地编写普通的 JavaScript。

## 基本类型

现在让我们利用 TypeScript 所提供的优势。

```
let sentence = "Proud cat owner"

function welcomePerson(title: string) {
  return "Welcome, " + title
}

console.log(welcomePerson(sentence)) // Welcome, Proud cat owner 
```

Enter fullscreen mode Exit fullscreen mode

所以在这里，我们只是给`title`参数添加了一个`string`类型的注释。这告诉我们，我们希望参数**只是**是一个字符串，**不是别的。如果你传递除了字符串以外的任何东西给那个参数，编译器会给你一个小错误。**

比如我把`let sentence`的值改成一个数组:

```
let sentence = [0, 1, 2]

function welcomePerson(title: string) {
  return "Welcome, " + title
}

console.log(welcomePerson(sentence)) // You did something illegal 
```

Enter fullscreen mode Exit fullscreen mode

我会在编译器中得到一个小错误。

```
error TS2345: Argument of type 'number[]' is not assignable to parameter of type 'string'. 
```

Enter fullscreen mode Exit fullscreen mode

## 界面

所以一个**接口**基本上是一个类型化的对象。接口定义了概念的属性和类型。

比如一个人。一个人有名字和年龄。这两处房产都与一个人有关。所以让我们创建一个`Person`接口。

```
interface Person {
  name: string;
  age: number;
  title: string;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，通过创建这个接口，我们现在拥有了一个概念所需的所有属性，以及它们的类型。它可以作为静态(或动态？)在`function welcomePerson()`中输入我们的`person`参数，我们就可以从接口中访问我们需要的任何属性。

我现在将创建一个定义我的对象，基于`Person`接口中的属性。

```
interface Person {
  name: string;
  age: number;
  title: string;
}

let travis = {
  name: "Travis",
  age: 22,
  title: "Proud cat owner",
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们将:

*   移除`let sentence`
*   在`function welcomePerson()`中将我方`person`参数的类型设置为`Person`(我方接口)
*   传入`let travis`作为函数`welcomePerson`的参数
*   使用`person.title`定位参数对象中的`title`属性

```
interface Person {
  name: string;
  age: number;
  title: string;
}

let travis = {
  name: "Travis",
  age: 22,
  title: "Proud cat owner",
}

function welcomePerson(person: Person) {
  return "Welcome, " + person.title
}

console.log(welcomePerson(travis)) // Welcome, Proud cat owner 
```

Enter fullscreen mode Exit fullscreen mode

保持饥饿，保持愚蠢🙏