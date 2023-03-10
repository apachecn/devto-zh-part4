# 成为有打字稿的逻辑学家

> 原文：<https://dev.to/codegram/becoming-a-logician-with-typescript-575i>

逻辑和计算是一枚硬币的两面——这被称为 **[库里-霍华德对应](https://en.wikipedia.org/wiki/Curry%E2%80%93Howard_correspondence)** 。

 <video src="https://media.giphy.com/media/BmmfETghGOPrW/giphy.mp4" loop="" class="centered">## 呃？

让我们在一个简短的旅程中探索这意味着什么，在旅程的最后，我的目标是让你相信，作为一名编写打字稿的开发人员，**你是以编写命题和证明**为生的！

纵观最近的历史，计算机科学家和逻辑学家似乎一直在独立地发现相同的概念——在一个领域发现的每个新概念似乎都巧妙地映射到另一个领域。

这种二元性一直是实用类型系统创新的巨大驱动力——例如，新的形式逻辑如[线性逻辑](https://homepages.inf.ed.ac.uk/wadler/papers/lineartaste/lineartaste-revised.pdf)的发现导致了 Rust 著名的借用检查器。

让我们使用 TypeScript 来探索日常生活中的字体，我们的面包和黄油，是如何从...另一边。

## 证明显而易见的事实

让我们看看我们在日常工作中可能使用的最简单的类型:`number`。

```
const n: number = 4 
```

此代码进行类型检查—它有一个类型和一个与该类型匹配的值。但是这和形式逻辑有什么关系呢？

我们可以把类型`number`看作一个逻辑**命题**，用简单的语言我们可以表达为**存在一个数**。很明显对吗？但是 TypeScript 不会相信我们*，直到我们证明它*。而这个证明就是*值 4* 。

让我们重新阅读上面的代码:

*   类型`number`是**命题**或声称*存在一个数*
*   值`4`是该命题或主张的有效**证明**
*   TypeScript 相信我们的证明，所以我们的程序进行类型检查！

## 这个，那个也是

如果简单类型声明该类型的值只存在...什么是元组？

```
const nameAndAge: [string, number] = ['Curry', 119] 
```

原来元组让我们鱼与熊掌兼得，就是做连词！让我们再次戴上逻辑学家的帽子:

*   元组类型`[string, number]`是**命题**，即*存在一个字符串，存在一个数*。
*   值`['Curry', 119]`是该命题的有效**证明**
*   这对于 TypeScript 来说已经足够令人信服了，所以我们的程序进行类型检查！

## 或者呢？

类似地，联合类型让我们把*或*个命题放在一起:

```
const nameOrAge: string | number = 'Curry' 
```

*   并集类型`string | number`是**命题**即*存在一个字符串或一个数字*(一个非排他性的`or`就像我们在编程中习惯的那样)
*   值`'Curry'`是该命题的有效**证明**
*   TypeScript 说当然，类型检查！

## 若此，则彼

从逻辑的角度来看，最有趣的类型脚本类型可能是...函数类型！

```
function add(a: number, b: number): number {
  return a + b
} 
```

函数代表**逻辑蕴涵**(如果`x`，那么`y`)。让我们看看如何:

*   函数类型`(a: number, b: number): number`是**命题**即*如果存在一个数`a`和一个数`b`，那么也存在第三个数*(返回值)。
*   实现`return a + b`是一个有效的**证明**，证明给定这两个数字，你可以有效地产生一个数字。

### 少即是多

您可能已经注意到，正如类型`number`有许多数字一样，函数`(a: number, b: number): number`也有许多实现！

一个可能的实现可以总是返回`42`，并且它仍然会进行类型检查，因为给定`a`和`b`，即使你没有使用它们，你也能够产生一个`number`。

这就是为什么，类型系统表达能力越强，你就越能*约束类型*(命题)，因此有效实现*越少*(证明)——因此类型检查器将拒绝更多无效实现！

## 泛指用...无商标消费品

> 所有的概括都是危险的，即使是这个。
> 
> *大仲马的儿子亚历山大*

泛型是如何发挥作用的？它们在逻辑上是什么意思？它们是一种概括主张的方式，因此我们不必关注不必要的细节。

```
function length<A>(array: Array<A>): number {
  return array.length
} 
```

在形式逻辑中，它们类似于命题的普遍量化(声称一个命题适用于所有事物、所有情况)。让我们从 TypeScript 的角度来看:

*   函数类型`(array: Array<A>): number`是**命题**即*对于任意类型`A`，如果存在一个`A`的数组，那么也存在一个数*(返回值，其长度)。
*   实现`array.length`是一个有效的**证明**，你可以有效地给定一个绝对任意类型的数组来产生一个数。

## 凝望深渊；永不言弃`never`

在任何一个很酷的形式逻辑里，都应该有骂人的方法。TypeScript 有用于此的`never`类型！让我们看看它在实践中是如何使用的。

```
function goOnForever(a: number): never {
  while (true) {
    console.log('I never end!')
  }
} 
```

类型`never`也被称为底部类型，它有**没有值**。这意味着我们不能**从这个函数中返回任何东西**(我的意思是什么也不返回，甚至是`undefined`或`null`)。简单地说，我喜欢把它翻译成命题**我们完蛋了**。

*   函数类型`(a: number): never`是**命题**，即*如果存在一个数`a`，我们就完蛋了*。
*   该实现从不返回任何值，因此无法进行进一步的推理。我们完蛋了。TypeScript 很高兴地进行类型检查，因为我们确实履行了我们的承诺:我们永远不会返回任何东西。

## 前途无量的月亮

最后，让我们来看一个不可能的类型(命题)，并尝试实现(证明)它:

```
function arbitrary<A>(): A { ... } 
```

什么？我们声称*对于任何类型 A，我们都可以得出该类型的值*。过分自信了吗？你将如何实现这个函数，这样你就可以创建一个类型为`number`、`Record<string, Password>`和`UserPreferencesFactory`的值，以及我在这个函数中抛出的其他类型为`A`的值？

这个功能肯定是不可能实现的...在声音类型系统中。但是你瞧，这个类型检查:

```
function arbitrary<A>(): A {
  return (null as unknown) as A
} 
```

啊哈！事实证明 TypeScript 并不可靠——大多数现代实用类型系统也是如此。这不一定是一件坏事，但它为我们提供了一个搞砸程序的主要途径。小心点！

Ambrose Bonnaire-Sergeant 写了一篇很有见地的文章，讲述了为什么现代类型系统中的不健全可能不是一件坏事。

## 结论

如果你喜欢这篇文章，并想了解更多关于这些想法的信息，我推荐你阅读菲利普·瓦德勒的论文[命题作为类型](https://homepages.inf.ed.ac.uk/wadler/papers/propositions-as-types/propositions-as-types.pdf)。它写得很好，很机智，甚至像我这样不擅长数学的人也能理解。

我还做了一些关于这个主题的演讲，以及让我们的程序更安全的表达类型系统的潜力(因此，我们的世界越来越依赖于这些程序)。看这里。

现在我要让你回到你的命题和证明，我的意思是，你的打字稿项目。

<small>Benjamin Lizardo 在[Unsplash](https://unsplash.com/photos/IZsbWBZ08cs)T3 拍摄的封面照片</small></video>