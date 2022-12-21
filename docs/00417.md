# Javascript 的一个更简单的咖喱

> 原文：<https://dev.to/prashanth1k/a-simpler-curry-for-javascript-j01>

这里有一个用 Javascript 实现 curry 函数的更简单的方法。

我们之前已经看到了 Javascript 中的[curry。这个概念的一个简单形式和应用将在下面展示-](https://techformist.com/currying-in-javascript/) 

```
const addThem = add.curry(2);
const addTotal = addThem(1);
console.log("addTotal: ", addTotal); // 3 
```

Enter fullscreen mode Exit fullscreen mode

或者，我们可以避免外部函数或库以及使用绑定的[..](https://techformist.com/curry-using-bind-javascript/) 

```
function add(x) {
  return function(y) {
    return y + x;
  };
}

const addEm = add(1);

console.log(addEm(2)); // 3 
```

Enter fullscreen mode Exit fullscreen mode

但是，有一种更简单的方法可以得到相同的结果。

我们只是用箭头函数来收集不同时间的论点。

```
const add = x => y => x + y;

const addEm = add(1);
console.log(addEm(2)); // 3 
```

Enter fullscreen mode Exit fullscreen mode

我们可以用不同的符号使代码更具可读性，以进行实际的编码-

```
const add = x => y => x + y;
console.log(add(1)(2)); // 3 
```

Enter fullscreen mode Exit fullscreen mode

当然，如果你不能一次完成所有的论证，你必须依靠前面提供的例子。