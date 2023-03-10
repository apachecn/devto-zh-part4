# 考虑递归:如何递归遍历 JSON 对象和文件系统

> 原文：<https://dev.to/wagslane/thinking-about-recursion-how-to-recursively-traverse-json-objects-and-the-filesystem-4oag>

[![](img/5684c7fff93b7c80cf35425316842875.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PnHgP4hN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qvault.io/wp-content/uploads/2019/09/photo-1527266237111-a4989d028b4b-1024x683.jpg)

我主要从事应用级的程序，所以我倾向于不经常使用递归。然而，我不时需要一个最好递归求解的函数。重要的是能够**认识到什么时候问题最好递归解决**，并且**能够在时机到来的时候写出它**。

## 什么是递归？

递归是一个**函数调用自己**的过程。

[![](img/7702b5ce7a9506dcd7de0ef2197b6bea.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--j4THBYqF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qvault.io/wp-content/uploads/2019/09/cut.jpg)

例如:

```
function printArrayRecursive(arr, i) {
  // base case, stop recurring
  if (i === arr.length){
    return;
  }
  console.log(arr[i])
  // call ourself with the next index
  recursive(arr, i+1)
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，*printarrayredrustic*打印列表中的一个元素，然后用下一个索引再次调用自己。每次对自身的连续调用都会打印下一个元素，依此类推。递归继续，直到到达**基本情况**。在我们的例子中，基本情况是索引等于数组的长度。

同样的函数在**迭代**的世界里看起来相当不一样，你可能更熟悉:

```
function printArrayIterative(arr){
  for (let i = 0; i < arr.length; i++){
    console.log(arr[i])
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在简单打印列表项的情况下，迭代方法更好，原因如下:

*   更容易阅读和理解
*   更少的内存使用—*递归函数将所有调用保留在堆栈上，直到到达基本情况*
*   更快的计算时间—*递归函数伴随着每步调用整个函数的开销*
*   如果递归中有错误，程序很可能会进入无限循环

## 那么为什么要使用递归呢？

所有的迭代程序都可以用递归来编写，所有的递归程序都可以用迭代来编写。这是因为两个系统都是 [turing complete](https://en.wikipedia.org/wiki/Turing_completeness) ，除非受到实现的限制。

<figure>[![](img/c8c752a8ae87f6cc7362d66f32935e99.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QwkOwGLy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qvault.io/wp-content/uploads/2019/09/mechanical-turing-machine-in-wood-vo8izckhif0mp4-shot0001_featured.png) 

<figcaption>[机械图灵机](https://hackaday.com/2018/03/08/mechanical-wooden-turing-machine/)</figcaption>

</figure>

选择递归而不是迭代的主要原因是简单性。

许多年前，许多编译器和解释器不支持迭代语法。 **For 循环根本不存在**。这是因为编写一个能够处理递归的解释器比编写一个支持循环的解释器要简单得多。

同样，即使编译器不支持循环，一些问题用递归函数解决也更简单。一个很好的例子是树遍历。我经常发现自己编写递归函数来查找任意 JSON 对象的每个属性，或者查看文件夹中的每个文件，该文件夹可以有无限个嵌套子文件夹。

## 例子

**递归打印一个 JSON 对象的所有属性:**

```
function printAllVals(obj) {
  for (let k in obj) {
    if (typeof obj[k] === "object") {
      printAllVals(obj[k])
    } else {
      // base case, stop recurring
      console.log(obj[k]);
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

递归打印一个文件夹及其子文件夹的所有文件名。

```
function printSubFiles(dir) {
  files = fs.readdirSync(dir);
  files.forEach(function (file) {
    absName = `${dir}/${file}`
    if (fs.statSync(absName).isDirectory()) {
      printSubFiles(absName)
    } else {
      // base case, stop recurring
      console.log(file)
    }
  });
}; 
```

Enter fullscreen mode Exit fullscreen mode

当试图弄清楚如何递归地编写一个函数时，想一想，

“我的基本情况是什么？”或者换句话说，**“什么应该阻止递归继续？”**

一旦解决了这个问题，剩下的函数只需要回答问题，

“我想用我现在的价值做什么？”

和

**“我如何调用自己来获得下一个值？”**

对于任何程序员来说，递归都是一个需要理解的重要原则，我希望这能帮助你变得更好！感谢阅读。

由莱恩·瓦格纳 [@wagslane](https://twitter.com/wagslane)

下载 q vault:[https://q vault . io](https://qvault.io/)

星我们的 Github:[https://github.com/q-vault/qvault](https://github.com/q-vault/qvault)

帖子[思考递归:如何递归遍历 JSON 对象和文件系统](https://qvault.io/2019/09/22/thinking-about-recursion-how-to-recursively-traverse-json-objects-and-the-filesystem/)最早出现在 [Qvault](https://qvault.io) 上。