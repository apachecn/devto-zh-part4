# 使用 React 学习 TypeScript 第 1 部分

> 原文：<https://dev.to/analizapandac/learning-typescript-with-react-part-1-3ohn>

⚡tl；理解什么是类型、类型注释、为什么使用它们以及如何使用它们可以帮助你在开发过程中发现错误，同时也提高代码质量和可读性。

两周前，我决定拿起 TypeScript，于是我通过创建 [TEDFlix](https://tedflix.netlify.com/) 开始学习它。它与 React 一起构建，从官方 [Tedx Talks](https://www.youtube.com/channel/UCsT0YIqwnpJCM-mx7-gSA4Q%20target=) Youtube 频道获取视频供你观看。为什么是 TEDFlix？这听起来很有趣，老实说，我已经没有主意了😛

在这个由两部分组成的系列中，我想分享我从这个项目中学到的东西。

在深入研究之前，让我给你一个简短的定义，什么是取自官方 TS 网站的 TypeScript (TS)。

> " TypeScript 是 JavaScript 的类型化超集，它编译成普通 JavaScript . "

现在，让我也给你一个高层次的应用程序的功能概述，以及如何反应方面的结构。

这是一个一页的应用程序，它有一个视频播放器，一个相关谈话的列表和一个搜索栏，这些都是作为独立的 React 组件构建的。在页面加载时，它从一个 JSON 文件中获取一个默认的谈话列表，而不是从 Youtube 中获取，以避免当页面被许多用户加载时超过 Youtube API 的每日使用配额。该应用程序仅在搜索谈话时与 Youtube 通信。

好了，我们开始吧。

### 一、项目设置

我用 Create React App 和 TypeScript
生成了这个项目

```
create-react-app my-app-name --typescript 
```

## **我是怎么在这个 app 里使用 TypeScript 的？**

因为我是 TS 初学者，所以我首先从学习语言的语法和特性开始*，特别是类型、类型注释和接口*。在第一篇文章中，我将只讨论类型和注释。

### **什么是类型？**

由于 TypeScript 是一种类型化语言，这意味着我们可以指定/注释变量、函数参数和对象属性的类型。

**从我自己的理解，类型是一个变量可以访问的所有属性和方法的符号/表示。**意思是如果我们给一个变量添加了一个 **`number`** 的类型，TS 编译器就知道这个变量有*并且只能访问一个数字有*的所有属性和方法。

[![](img/03aaed3bd47fb776d44b501e28446e7f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mxu1vjZi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/am8chbwe2wbt9n8hnft6.gif)

> 使用此格式 **`:type`** 对变量、参数或属性进行类型注释。
> 比如说`let name: string`。

TS 中有许多可用的类型，但是在这个应用程序中，这些是我用过的类型:

1.  **原始类型** -数字、字符串、布尔型
2.  **数组**——通过在类型后添加`[]`(例如`let names: string[]`)
3.  视频-我创建的一个接口或者自定义类型来表示一个视频对象的属性

参见[此处](https://basarat.gitbooks.io/typescript/docs/types/type-system.html)获取更全面的类型列表。

### **我们为什么关心标注类型？**

1.  类型是你能拥有的最好的文档形式之一。这对下一个必须阅读你的代码的开发者*非常有帮助，他也可能是未来的你*。
2.  它帮助 TypeScript 编译器帮助你。如果你将一个变量注释为一个数字，几行后又给它赋了一个字符串值，编译器会给出以下错误。

```
let x: number = 123;
x = '123'; // Error: cannot assign a `string` to a `number` 
```

即使我们从变量`x`中移除`:number`注释，编译器也会给出同样的错误，因为 **TypeScript 是智能的** *。

> *如果你用一个值声明一个变量而没有指定类型， **TypeScript 会根据赋给它的初始值计算出变量的类型。**
> 
> 这就是所谓的**式推理**。

那么，如果类型推理存在，为什么我还要费心去注释变量呢

**类型推断并不总是有效。**

一个示例场景是延迟变量的初始化，如下所示。

```
let age;

// a few lines later
age = 12; 
// The compiler will NOT give an error even 
// if we assign a non-numeric value to `age`
age = 'Ana'; 
```

如果您延迟变量初始化而没有给它添加类型注释，变量将自动有一个类型 **`any`** 与之关联，因为 TS 编译器不知道`age`以后会有什么样的属性。

**应尽可能避免使用类型为`any`的变量。**这违背了 ts 类型系统背后的理念，即我们希望在开发过程中捕捉尽可能多的错误。为什么？因为，编译器无法对`any`的类型进行错误检查。

**记住，使用类型来帮助 TS 编译器帮助你。**

### **类型标注在 app 中是如何使用的？**

除了在变量声明中使用类型，我还在函数声明中使用了注释。

函数可以通过以下方式进行注释:

#### 1。箭头功能

```
const add = (a:number, b:number):number => {
 return a + b;
}

// or
const add: (a: number, b: number) => number = (a, b) => {
  return a + b;
}; 
```

#### 2。非箭头函数

```
function add(a:number, b:number):number {
 return a + b;
} 
```

在这两个例子中，如果函数返回不是数字*的字符串或任何类型，编译器将给出错误，因为它期望的是数字*。

为了获取频道的视频，我创建了一个名为 **`fetchChannelVideos`** 的函数，它接受一个布尔标志，指示是否从 JSON 文件中获取默认视频和一个搜索查询字符串。这两个都是可选参数(通过在属性名后添加 **`?`** )，表示为一个接口。稍后我会解释什么是接口，但是现在让我们仔细看看函数是如何被注释的。

```
interface FetchVideosArguments {
 shouldUseDefaultVideos?: boolean;
 searchQuery?: string;
}

export const fetchChannelVideos: (
 args: FetchVideosArguments
) => Promise < Video[] > = async ({
 shouldUseDefaultVideos = false,
 searchQuery
}) => {}; 
```

左侧赋值运算符(`=`)，

```
const fetchChannelVideos: (args: FetchVideosArguments) => Promise <Video[]> 
```

*我们正在注释被声明为**的变量 **`fetchChannelVideos`** ，而不是被赋值为*** 的函数。我们告诉 TS 编译器，这个变量将被赋予一个函数，它带有这些类型的参数和返回值。

而`=`的右边部分是分配的功能。

```
async ({
 shouldUseDefaultVideos = false,
 searchQuery
}) => {}; 
```

要注释函数本身，我们必须指定它的参数和类型以及返回值。

> *那么为什么我没有将函数注释为分配给 **`fetchChannelVideos`** ？*因为打字稿很聪明。

由于我将函数赋给了一个带注释的变量，因此可以推断出该函数将具有相同的参数名称和类型以及返回值，否则如果我添加或指定不同的参数名称或返回值，它将会出错。

[![](img/149745d33b2d553593a5717e8747b8bc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--i-rpazSH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x7syqxtc3he7ih85bjmw.gif)**函数参数和返回值被推断*

**然而**，如果我直接导出函数而没有将它赋给变量，那么我必须像下面这样注释它。

```
export async function fetchChannelVideos({
 shouldUseDefaultVideos = false,
 searchQuery
}: FetchVideosArguments): Promise<Video[]> {
 // function body
} 
```

好了，现在我们有了一个关于什么，为什么和如何类型注释的概念，还有最后一个问题。

### **我们在哪里使用类型？**

答案是:**处处**。通过使用 TypeScript，无论您喜欢与否，应用程序中的每个数据都将有一个与之关联的类型。

## **关闭思绪**

到目前为止，我只接触了基础知识，但我已经看到了它的帮助，在编写代码时看到编译器给出的错误，而不是在运行应用程序时发现它们，节省了我很多时间。

### **学习资源**

如果你想了解更多，并加入我的旅程，这里有一些可用的资源，可以帮助你开始。

[5 分钟内打字完毕](https://www.typescriptlang.org/docs/handbook/typescript-in-5-minutes.html)

[打字稿深潜](https://basarat.gitbooks.io/typescript/)

[正式打字稿文档](https://www.typescriptlang.org/docs/handbook/basic-types.html)

* * *

我在 Netlify 部署了这个应用:[https://tedflix.netlify.com/](https://tedflix.netlify.com/)

很快会分享项目的 Github repo。

* * *

***更新:06/07***
关于接口的最后一部分上来了🎉

[![analizapandac image](img/01116b53a5580c3eee186c68441a0f2b.png)](/analizapandac) [## 使用 React 学习 TypeScript 第 2 部分(接口是什么、为什么和如何)

### 安娜丽莎潘达克 7 月 6 日 196 分钟阅读

#typescript #react #javascript #webdev](/analizapandac/learning-typescript-with-react-part-2-the-what-why-and-how-of-interfaces-1033)

* * *

如果你对下一步该怎么做有任何反馈、建议或指点，请在下面的评论中告诉我。提前感谢。

干杯！