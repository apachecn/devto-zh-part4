# 这就是为什么我讨厌打字稿

> 原文：<https://dev.to/michaeljota/this-is-why-i-hate-typescript-3da0>

> 这是一篇讽刺文章，关于一些反对打字稿的论点。虽然它们中的大多数被夸大了，但它们都代表了一个或几个人的观点。

## 打字稿不标准

Typescript 只是有史以来最棒的语言 JavaScript 之上的一大堆垃圾，它甚至没有试图遵循 JS 的标准。我会举例说明 TS 是如何不遵循 es 标准的。

### 模块

TS 有自己的模块系统，称为命名空间。你可能会问什么是名称空间？这是微软发明的完全忽略 ES 标准模块的东西。我不在乎当 TS 第一次宣布的时候 ES6 模块没有被真正定义，微软应该知道！还有，我不管 TS 现在是否支持 ES 模块，名称空间应该从来没有过，应该消失！

### 类

就像 TS 有名称空间一样，它们有自己的方式来声明一个类。

Typescript 中的一个类:

```
class Foo {
  bar = 0;
  baz = 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

Javascript 中的一个类:

```
function Foo() {
  this.bar = 0;
  this.baz = 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

你可能会说我在写 ES5 风格的类，但即使是 ES2015，JS 看起来也要好得多！

```
class Foo {
  constructor() {
    this.bar = 0;
    this.baz = 0;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

当然，你也可以说当 TS 引入类时，JS 类的提议还没有定义。但是为什么他们使用类字段声明呢？他们不标准！ [<sup>【尚未】</sup>](https://github.com/tc39/proposal-class-fields) 。

### 私有属性

好吧，JS 没有私有成员，但是当它有私有成员时，它会类似于:

```
class Foo {
  constructor() {
    this.#bar = 0;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，TS 确实支持 privare 成员，但在微软语言中看起来如何？

```
class Foo {
  private bar = 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

丑！他们甚至都没有在努力！[他们不是](https://github.com/microsoft/TypeScript/wiki/Roadmap#future)吗？这还不够，TS 中的“私有”成员并不是真正的私有成员，因为您可以用括号语法来访问它们！

## Typescript 不是 JavaScript

取任何当前的 ES 代码，尝试用一个全新的 TS 项目来编译它。你做不到。因为 Typescript 不是 JavaScript！甚至不是一个简单的阶乘函数。

在 JS:

```
function factorial (n) { 
  if (n == 0) return 1;

  return n * factorial (n-1); 
} 
```

Enter fullscreen mode Exit fullscreen mode

在 TS:

```
function factorial (n) { 
  if (n == 0) return 1;

  return n * factorial (n-1); 
} 
```

Enter fullscreen mode Exit fullscreen mode

你现在有两个错误，因为 TS 不能理解你在做什么。你必须用你能理解的语法来写。

```
function factorial (n: number): number { 
  if (n == 0) return 1;

  return n * factorial (n-1); 
} 
```

Enter fullscreen mode Exit fullscreen mode

你有它 TS！现在你，很可能是世界上任何一个只阅读签名的人，将会知道函数应该返回什么，以及参数类型应该是什么。我不想写那个。为什么 TS 不能理解我返回的是同一个最终会返回一个数字的函数？此外，如果我想让别人理解我的代码，我会对它进行注释，或者添加单元测试。这是任何人理解代码所需要的。此外，这是一个基本功能，有什么需要解释的呢？你不可能无知到不知道函数递归纯函数是做什么的！

你可能会说，我必须更改编译器选项，并禁用隐式 any 来消除这个错误。但是我希望 TS 能更好地理解我的代码。

## Typescript 的创作者对编程一窍不通

我就开始说微软是万恶之父，Typescript 是微软创造的。正如我上面解释的那样，它不遵循任何标准，只是为了消除我们所知的 web 开发而创建的。永远记住微软 moto: [拥抱，延伸，熄灭](https://en.wikipedia.org/wiki/Embrace,_extend,_and_extinguish)。那发生在 20 年前，但是不要犯错，公司不会改变！微软还是那个想控制一切的老公司！

其次，如果你查找谁是 Typescript 的主要开发者之一，很可能是多年来塑造它的那个人，你会发现[安德斯·海尔斯伯格](https://en.wikipedia.org/wiki/Anders_Hejlsberg)。他懂什么编程？是一个对标准一无所知的老人。他刚刚开发了像 [Turbo Pascal](https://en.wikipedia.org/wiki/Turbo_Pascal) 和 [Delphi](https://en.wikipedia.org/wiki/Delphi_(IDE)) 这样的程序。他所知道的关于语言的唯一一件事，是因为他是 C#的首席架构师。我相信社区里的任何人都会做得更好。

## 人们用 Typescript 只是因为习惯了 OO 语言

更喜欢使用 Typescript 而不是 JavaScript 的开发人员只是对 JavaScript 是一种函数式编程感到沮丧，他们宁愿忽略 JavaScript 的所有好特性，而用传统的面向对象的方式编写，成为前端开发人员也想碍事。你必须打破枷锁，兄弟！你需要理解 JS 给你的自由，并拥抱它！只有用老方法使用 JavaScript，你才能做到这一点。

好的。在这一节我会很认真，因为这几乎是真的。我所知道的大多数来自面向对象语言的开发人员，如 C#或 Java，会试图用他们习惯使用这些语言的方式来使用 TS。这是一个真正的问题。TS 不是 JS 上面的魔法糖。你真的需要理解 JS，才能理解 TS 到底在为你做什么。如果你不这样做，你可能会写更多的代码，使用反模式，并产生更多的错误

## 你不能调试类型脚本

你使用 Webpack、Parcel 或任何生成源地图的工具吗？为什么？你必须停下来，只写普通的旧 JavaScript。因为如果你向下编译，你可能需要其他东西来调试你的代码。如果你写的代码只能用 IE 的开发工具来调试就更好了。

## 巴别塔好太多了

我已经说过 Typescript 不是 Javascript。但是巴贝尔是。是编译成标准 Javascript 的标准 JS。随便对比一下:[打字稿](https://www.typescriptlang.org/play/index.html#code/MYGwhgzhAEBiD29oG8BQBIARmATtAvNAMwDcG2AXgcWQL5A) vs [巴别塔](https://babeljs.io/repl/#?babili=false&browsers=&build=&builtIns=false&spec=false&loose=false&code_lz=MYGwhgzhAEBiD29oG8BQ1oCMwCdoF5oAGAbnSzAC8DiyBfVIA&debug=false&forceAllTransforms=false&shippedProposals=false&circleciRepo=&evaluate=false&fileSize=false&timeTravel=false&sourceType=module&lineWrap=true&presets=es2015%2Creact%2Cstage-2&prettier=false&targets=&version=7.5.5&externalPlugins=)

## Typescript 只用于有角度的

这也是我不喜欢有棱角的原因。我从未见过其他项目使用 TS。我再为你重复一遍[我](https://facebook.github.io/create-react-app/docs/adding-typescript) [有](https://medium.com/the-vue-point/plans-for-the-next-iteration-of-vue-js-777ffea6fabf#1855) [从未](https://nextjs.org/learn/excel/typescript) [见过](https://github.com/nestjs/nest) [另一个](https://github.com/ant-design/ant-design) [项目](https://github.com/apollographql?utf8=%E2%9C%93&q=&type=&language=typescript) [使用](https://github.com/typeorm/typeorm) [打字稿](https://www.typescriptlang.org/)。[没人](https://2018.stateofjs.com/javascript-flavors/overview/)喜欢打字稿。

## 流量更好

因为，当你可以在`.js`文件中写类型时，为什么你想要另一个文件扩展名来明确声明你不写 JS 呢？此外，它的支持正在变得更好，更多的[项目](https://github.com/facebook/jest/pull/7554)正在用 Flow 编写。

# 你不应该使用打字稿

如果...你不顺从。如果你没有注意到，这是一个讽刺的帖子，关于那些抱怨 TS 的人。我并不是说你应该在任何地方都使用 TS，我确信如果你想做一个待办事项应用，用 JS 会很安全。但是根据我的经验，如果你和三个以上的人一起工作，TS 将会帮助多于伤害。而且不得不说，在你不了解 JS 的时候，用 TS 很伤人。但我不认为这是打字组的问题，而是我们的问题。

作为开发人员，我们需要了解每一种语言都是一种工具，可以帮助我们做一些事情。有时候需要锤子，有时候需要星键。不是每种语言都适合所有的东西，也许当你习惯使用星号键时，你不喜欢使用锤子。但是不能说每个使用锤子的人都是错的，或者他们应该使用星号键。

> 你不妨学习一下如何使用锤子。