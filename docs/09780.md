# 我在 Javascript 中递归、闭包和回调的冒险

> 原文：<https://dev.to/ahrke/my-adventure-with-recursions-closures-and-callbacks-in-javascript-23mg>

[![victory](img/18bbf82eb11576759376ba4494017444.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6Z17bjwr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1452697620382-f6543ead73b5%3Fixlib%3Drb-1.2.1%26ixid%3DeyJhcHBfaWQiOjEyMDd9%26auto%3Dformat%26fit%3Dcrop%26w%3D2850%26q%3D80) 
照片由 Nghia Le 在 Unsplash

## 我是谁？我的体验是什么？

大家好，我是戴夫。一个自学成才的软件工程师，知识上的差距来自于不知道我不知道的东西。最近，我参加了一个训练营，目的是提高和巩固我的技能，并填补这些空白。

这篇文章将涵盖我对我们面临的挑战的解决方案。这不是最有效的解决方案，而且有些地方可能不具备最佳实践。我还在学习。这是我旨在提高我对递归、闭包和回调的理解。

## 我正在解决的问题是什么？

我的训练营提供了一个挑战，当地市场雇佣我们开发一个项目。他们有一个交易，顾客可以免费退还他们购买的康普茶(好吧，我加了康普茶，没有指定的饮料类型)。交易过程如下:

*   2 瓶=> 1 瓶免费
*   4 个瓶盖=> 1 个免费瓶子
*   2 美元=> 1 瓶

面临的挑战是开发一个程序，帮助客户计算他们可以从初始投资中获得的瓶子总数。例如，20 美元的投资将净得 35 瓶。

#### 输入= >输出

| 花费了美元 | 康普茶总瓶数 |
| --- | --- |
| Ten | Fifteen |
| Twenty | Thirty-five |
| Thirty | Fifty-five |
| Forty | Seventy-five |

#### 最终输出

现在，它并不停留在返回总瓶数上。在弄清楚如何解决这个问题后，我们的任务是打印出各种信息，如剩余的瓶盖数量，以及通过回收瓶子获得了多少瓶子。样本输出:

```
Total Bottles:      35
Remaining Bottles:  1
Remaining Caps:     3
Total Earned From:
  Bottles:          37
  Caps:             18 
```

## 递归

最初，我怀疑自己是否需要使用递归...毕竟，我不是递归的忠实粉丝。然而，这是一个我想更擅长的概念，所以我需要找到一种使用递归来解决这个问题的方法。

首先，我们将解决从给定投资中返回瓶子总数的问题。

```
let bottlesFromInitialInvestment = invested / 2;
let bottlesEarned = totalEarnedFromBottles(bottlesFromInitialInvestment, bottlesFromInitialInvestment); 
```

`totalEarnedFromBottles(bottles,caps)`是一个独立的函数，神奇的事情就发生在这里。这个助手函数将计算我们从一组给定的瓶子和瓶盖中赚了多少瓶。它接收瓶数和瓶盖数，并返回从这些初始值中获得的金额。

#### 进`totalEarnedFromBottles(bottles,caps)`我们走！

这就是我想利用递归的地方。就现实世界的使用和应用而言，这是一个我仍在努力的概念。然而，我知道在任何递归解决方案的开始...我们需要建立*基础案例*。递归的定义:“一个不断调用自己直到不调用为止的函数”，*基例*帮助确定'*，直到不调用为止*。这是我们函数的输入导致它停止调用自己的时候。

#### 在我继续之前，什么是递归？

没错。之前简单提到过，就是当“一个函数调用自己，直到不调用为止”。关于“直到它不”的部分是递归最重要的部分，因为缺少出口，我们的函数将填满调用堆栈并导致堆栈溢出。

```
const recurse() => {
  return recurse()
}

recurse()  // Error: Stack Overflow 
```

所以我们需要一种方法让函数知道停止调用自己。这是*基础案例*。这可以被认为是函数只能返回一个逻辑答案的点。通常这是最简单的场景，也是我们的递归应该如何工作的一个很好的例子。例如

```
const sum = (arr) => {
  if (arr.length === 1) return arr[0];

  return arr[0] + sum(arr.slice(1));
} 
```

这里我们有一个对数组中的值求和的函数。我们的基本案例是`if (arr.length === 1) return arr[0]`语句。如果我们的`sum()`函数接收一个数组，我们想要返回它。这时我们的函数知道停止调用自己。当它到达时，它就结束了。

现在，我们如何首先把它带到终点？这里我们使用的技巧是使用 Array.prototype.slice()删除给定数组的第一个元素，并将其传递回`sum()`。这就是我们如何递归`sum()`。为了对所有的值求和，我们需要将每个值相加，所以返回语句将是给定数组的第一个元素，加上`sum()`将从它的下一次迭代中返回的内容。

```
sum([1,2,3,4])  // 10

just to break down each iteration, it'd look something like this...

sum([1,2,3,4]) => return 1 + sum([2,3,4])
  sum([2,3,4]) => return 2 + sum([3,4])
    sum([3,4]) => return 3 + sum([4])
      sum([4]) => return 4  // since arr.length === 1, we return arr[0]

    sum([3,4]) = 3 + 4 // 7
  sum([2,3,4]) = 2 + 7 // 9
sum([1,2,3,4]) = 1 + 9 // 10 
```

不管怎样，希望有帮助。如果没有，有很多很好的资源可以让[学习递归](https://blog.angularindepth.com/learn-recursion-in-10-minutes-e3262ac08a1)

#### 回到我们的问题

我想，停止递归的关键是当我们没有足够的瓶子和瓶盖来赚一瓶，所以...

```
if (bottles < 2 && caps < 4) return 0; 
```

酷毙了。我们已经解决了这个问题。
接下来...*递归案例*。这决定了我们应该如何以及何时在函数内部调用函数。我们希望函数每次被调用时都返回的是什么？没错，我们可以从收到的给定数量的瓶子和瓶盖中赚取的瓶子数量。嗯，那很简单:

```
let earnedFromBottles = Math.floor(bottles / 2);
let earnedFromCaps = Math.floor(caps / 4);

let totalEarned = earnedFromBottles + earnedFromCaps; 
```

一点也不差，我们已经赚到了。我们可以再次调用我们的函数，给它我们刚刚赚到的瓶子数量。然而，在我们这样做之前，还有一个问题是给多少个上限，并等待...可能有没用过的瓶子吧？嗯...因此，我们需要计算在我们的瓶子和瓶盖交易后剩余的瓶子和瓶盖，然后将其添加到下一个函数递归参数中。另外，让我们考虑一下我们的函数应该返回什么。我们希望它返回从给定输入中获得的金额，对吗？

```
...
let remainingBottles = (bottles % 2) + totalEarned;
let remainingCaps = (caps % 4) + totalEarned;

return totalEarned + totalEarnedFromBottles(remainingBottles, remainingCaps); 
```

唷，看来我们成功了。这看起来应该有用。所以我做了些测试来确认。令人高兴的是，我得到了我应该得到的。所以我们继续前进！哦，是的，完整的。

```
const totalEarnedFromBottles = (bottles, caps) => {
  if (bottles < 2 && caps < 4) return 0;

  let earnedFromBottles = Math.floor(bottles / 2);
  let earnedFromCaps = Math.floor(caps / 4);

  let totalEarned = earnedFromBottles + earnedFromCaps;

  let remainingBottles = (bottles % 2) + totalEarned;
  let remainingCaps = (caps % 4) + totalEarned;

  return totalEarned + totalEarnedFromBottles(remainingBottles, remainingCaps);
} 
```

## 关闭

[![closure-russian-nesting-doll](img/d752b74fdb1a90f2bf666024dc184931.png)](https://i.giphy.com/media/ywDAO6ONmOSVG/giphy.gif)

唷！我们解决了问题的最大部分！对吗？我的意思是，我们主要需要某项投资的净瓶数...对吗？嗯，还有一个问题是我们如何打印出想要的数据。

我们需要打印剩余的瓶子数量、剩余的瓶盖数量、从瓶子中获得的瓶子数量以及从瓶盖中获得的瓶子数量。这意味着我们需要以某种方式跟踪它们，因为我们通过我们的函数递归，并将信息返回给我们的主函数。

起初，我很难找到一种方法来做这件事。我尝试将一个数组附加到`totalEarnedFromBottles()`的返回对象的末尾。这个想法是，我可以在每次迭代中推动“剩余瓶子/瓶盖”的价值...然而，事情变得一团糟。回头看，这可能是由于执行不力。然而...我很感激无论我做了什么都没有成功，因为这给了我一个练习使用闭包的机会。

不管怎样，最后我想起我们最近学过闭包，所以我又读了一遍。我一直坚持的关于闭包的想法是，它们可以*保存*一个变量，并且它的值不会在它的调用结束后被扔进垃圾中。现在，它的实际工作方式要比这个复杂一些，但是这个简化的视图让我可以使用闭包。

#### 呃戴夫...什么是终结？

返回可以访问外部作用域函数属性的函数的函数。这个内部函数被返回或传递给一个单独的变量或函数。这种舞蹈使我们能够在不使用全局属性的情况下传递属性。
一个例子

```
const closureCreator = () => {
  let count = 0;

  return () => {
    return count++;
  }
}

let counter = closureCreator();

console.log(counter())  // 0
console.log(counter())  // 1
console.log(counter())  // 2 
```

很酷，是吧？所以使用闭包，我想我们可以通过在递归的每次迭代中调用闭包来跟踪数据。

这是我想到的:

```
const closureBottles = () => {
  let earnedFromBottles = [];
  let earnedFromCaps = [];
  let remainingBottles = [];
  let remainingCaps = [];

  return (frBottles, frCaps, remainingBotts, remainingCps) => {
    earnedFromBottles.push(frBottles);
    earnedFromCaps.push(frCaps);
    remainingBottles.push(remainingBotts)
    remainingCaps.push(remainingCps)

    return [earnedFromBottles, earnedFromCaps, remainingBottles, remainingCaps];
  }
} 
```

为每个数据块初始化一组数组。我们的 returning 函数获取我们从瓶子和瓶盖中获得的瓶子数量，以及两者的剩余数量。returning 函数用传入的值更新每个数组，然后将集合作为数组返回。

斯威特。我们结束了...现在我们如何使用它？

## 回调

[![callback-dog](img/719518d6c1a15798b1e33501c57eb12e.png)](https://i.giphy.com/media/4N8ZuLCehKnWylrQFm/giphy.gif)

没错！复试！在这个疯狂的开发者世界里，我们最好的伙伴之一。我们将把闭包函数传递给我们的`totalEarnedFromBottles()`。所以我们需要修改我们的`totalEarnedFromBottles()`,将回调作为它的一个参数，然后用我们从每次迭代中获得的数据调用它。

#### 等待，等待，等一下...戴夫...什么是回调？

哦，对了，如果你不确定什么是*回拨*，我会尽力帮你。再说一遍，我尽可能简化，因为有许多资源在教授这个令人敬畏的工具方面有更大的技巧。

*回调*是作为参数传递给另一个函数的函数(通常称为“高阶函数”)。高阶函数可以使用这个回调来执行某些任务。

首先，我在主函数中初始化我们的闭包(顺便说一下，它是`poppinBottles()`)，然后将它传递给我们对`totalEarnedFromBottles()` :
的调用

```
 let tracker = closureBottles(0,0);

  let bottlesEarnedTotal = totalEarnedFromBottles([bottlesInitial,0],[bottlesInitial,0], tracker); 
```

接下来，我们修改`totalEarnedFromBottles()` :

```
const totalEarnedFromBottles = (bottles, caps, callback) => {
  if (bottles[0] < 2 && caps[0] < 4) {
    callback(undefined, undefined, bottles[0], caps[0]);
    return 0;
  } 

  ...

  callback(earnedFromBottles, earnedFromCaps);

  return newBottles 
    + totalEarnedFromBottles([(newBottles + remainingBottles),earnedFromBottles], 
      [(totalCaps),earnedFromCaps], 
      callback);
} 
```

现在，通过我们的递归，每次迭代，`tracker()`(像超级英雄一样伪装成*`callback()`)将被调用我们从瓶子和瓶盖中获得的金额，然后它会将新值推入各自的数组中。我们只需要在最后加上每一种的剩余量，所以我们只需要在不能再换任何瓶子的时候打电话给`tracker()`。(在`if () {}` *基础案例*中找到)*

 *回到主函数，我们从我们的`tracker()`中获取所有这些值——好孩子`tracker()`🐕️，你-然后打印出来给我们伟大的客户！

```
let tempArraysOfBottleInfo = tracker().map(arr => arr.filter(e => e !== undefined).reverse()[0]);
  let [ earnedFromBottles, earnedFromCaps, remainingBottles, remainingCaps ] = tempArraysOfBottleInfo;

  let bottlesTotal = bottlesEarnedTotal + bottlesInitial;

  console.log(`
    Total Bottles:      ${bottlesTotal}
    Remaining Bottles:  ${remainingBottles}
    Remaining Caps:     ${remainingCaps}
    Total Earned:
      Bottles:          ${earnedFromBottles}
      Caps:             ${earnedFromCaps}
  `); 
```

`tempArraysOfBottleInfo`只从每个数组中获取我们想要的值。使用 map，我们遍历`tracker()`，清除未定义的值(因为每次我们调用该函数时，它会自动将一些值推送到它的数组中，甚至这个调用本身也会获取值)，然后从过滤后的数组中，我们反转它，并获取第一项。

之后，我们创建变量来保存各自的值，并为我们的客户打印出信息。瞧啊。

## 这些都是必要的吗？我能解决这个问题的另一种方法是什么？

不。所有这些步骤都绝对没有必要。你可以让递归函数接受一个对象，然后返回同一个对象。在每次迭代中，您只需更新每个值。把那个还回去，还有多尼斯基！

不管怎样，谢谢你坚持和我在一起！感谢你抽出时间。我知道我可能是一个巨大的分散的大脑，但这就是为什么我感谢软件工程和计算机科学。它帮助我不那么分散。那是下一次的话题。目前...这是完整的代码。祝你愉快！传递爱和尊重。

```
let investing = process.argv[2];

const totalEarnedFromBottles = (bottles, caps, callback) => {
  if (bottles[0] < 2 && caps[0] < 4) {
    callback(undefined, undefined, bottles[0], caps[0]);
    return 0;
  } 

  let remainingBottles = bottles[0] % 2;
  let newBottles = Math.floor(Math.floor(bottles[0] / 2) + (caps[0] / 4))
  let totalCaps = (caps[0] % 4) + newBottles;

  let earnedFromBottles = Math.floor(bottles[0] / 2) + bottles[1];
  let earnedFromCaps = Math.floor(caps[0] / 4) + caps[1];

  callback(earnedFromBottles, earnedFromCaps);

  return newBottles 
    + totalEarnedFromBottles([(newBottles + remainingBottles),earnedFromBottles], 
      [(totalCaps),earnedFromCaps], 
      callback);
}

const poppinBottles = (invested) => {
  let bottlesInitial = invested / 2;

  let tracker = closureBottles(0,0);

  let bottlesEarnedTotal = totalEarnedFromBottles([bottlesInitial,0],[bottlesInitial,0], tracker);

  let tempArraysOfBottleInfo = tracker().map(arr => arr.filter(e => e !== undefined).reverse()[0]);
  let [ earnedFromBottles, earnedFromCaps, remainingBottles, remainingCaps ] = tempArraysOfBottleInfo;

  let bottlesTotal = bottlesEarnedTotal + bottlesInitial;

  console.log(`
    Total Bottles:      ${bottlesTotal}
    Remaining Bottles:  ${remainingBottles}
    Remaining Caps:     ${remainingCaps}
    Total Earned:
      Bottles:          ${earnedFromBottles}
      Caps:             ${earnedFromCaps}
  `);

  return bottlesTotal;
}

const closureBottles = () => {
  let earnedFromBottles = [];
  let earnedFromCaps = [];
  let remainingBottles = [];
  let remainingCaps = [];

  return (frBottles, frCaps, remainingBotts, remainingCps) => {
    earnedFromBottles.push(frBottles);
    earnedFromCaps.push(frCaps);
    remainingBottles.push(remainingBotts)
    remainingCaps.push(remainingCps)
    return [earnedFromBottles, earnedFromCaps, remainingBottles, remainingCaps];
  }
}

poppinBottles(investing); 
```*