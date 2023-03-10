# 简化的退货单

> 原文：<https://dev.to/dillionmegida/return-statement-simplified-2l6j>

return 语句是计算一个值并将其附加到函数的语句。当这样的函数被调用时，它们返回被评估的值。

请看这个例子，

```
let a = 5;
let b = 5;
let c = a + b;
console.log(`a: ${a}, b: ${b}, c:${c}`);
//Output
//a: 5, b: 5, c: 10 
```

Enter fullscreen mode Exit fullscreen mode

return 语句的工作方式类似于上面的程序。当调用`a`时，它返回 *5* 作为值。呼叫`a`和呼叫`5`是一回事。你可以把`5`换成`a`，在任何你想用`5`的地方。

Return 语句主要用在函数中。他们和`console.log`不一样。Console.log 将从它的参数中输出评估值到控制台，但是`return`语句将把该值附加到函数中。

```
// return
function write1() {
    return "I will be attached to the write function";
}

// console.log
function write2() {
    console.log('I will be outputted');
} 
```

Enter fullscreen mode Exit fullscreen mode

调用`write1()`函数不会做太多工作，因为值只附加到函数上，没有输出它的动作。
调用`write2()`函数会在控制台上显示参数。

从第一个节目开始，我们可以把`write1()`比作:

```
function write1() = 'I will be attached to the write function';
// This is not the syntax for creating functions,
// for explanation purposes. 
```

Enter fullscreen mode Exit fullscreen mode

调用 write1()与调用“我将被附加到 write 函数”是一回事。

return 语句的另一个用例可能是:

```
function getAgeFor(yearOfBirth) {
    let currentYear = new Date().getFullYear();
    return currentYear < yearOfBirth ?
        'But dude, this is not possible' :
        currentYear - yearOfBirth;  
} 
```

Enter fullscreen mode Exit fullscreen mode

仔细看这个节目。当按预期使用`yearOfBirth`参数调用`getAgeFor`函数时，会得到 currentYear，然后使用三元运算符检查该函数返回什么。如果`currentYear`小于`yearOfBirth`，则函数返回一个值，否则返回不同的值。
记住，这不会输出值。

就像`a`被`c = a + b`用来获得`c`一样，`getAgeFor`也可以像
一样被使用

```
let myYearOfBirth = 1500;
if(typeOf getAgeFor(myYearOfBirth) === 'number') {
    console.log(`My current age is ${getAgeFor(myYearOfBirth)}`);
} else {
        console.log(getAgeFor(myYearOfBirth));
} 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们必须确保字符串('我的当前年龄是')是一个合适的数字，因为我们的`getAgeFor`函数要么返回一个字符串('但是老兄... '))或一个数字(currentyearyearofbirth)。然后，这个 if 语句可以用来输出包含从我们的函数中获得的估计年龄的字符串。

我还想让你知道，返回语句结束了一个函数的执行，也就是说，这个函数的前面的代码永远不会到达。

你可以在这里阅读更多关于 return 语句的内容: [return - Javascript | MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/return)

我真的希望这篇文章能教会你一些东西。
大家有什么问题、更正或者投稿，请在评论区分享。

谢谢大家！