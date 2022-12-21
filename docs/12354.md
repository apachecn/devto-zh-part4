# JavaScript 闭包:简单的解释。

> 原文：<https://dev.to/kedar9/closures-a-simple-explanation-1h0g>

没有任何花哨的介绍，让我们直接跳到`closure`是什么。

简单来说， **Closure 是一个内部函数，它会记住它在**中创建的环境。
把它想象成一个*感知的*函数可以从外部函数访问值(和参数)。

闭包的强大之处在于它能够读取和操作其外部函数的数据。

这是一个非常简单的结束的例子。把它想象成一个应用程序的代码。我们的目标是提示用户在每第三次访问时对应用程序进行评级。

```
function promptRating() {
  var appUsage = 0;

  return function() {
    appUsagae++;
    if (appUsage % 3 === 0) console.log('Please rate the app.');
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

`promptRating`是返回内部函数的函数。这个内部函数是一个闭包。它记住并从外部函数访问变量`appUsage`。

要查看它的运行情况:

```
// Init the outer function
var prompt = promptRating();

// Call `prompt` in regular workflow.
// If this is a multiple-of-3 visit, the user will be prompted to rate the app.
prompt(); // No Output
prompt(); // No Output
prompt(); // Output: Please rate the app. 
```

Enter fullscreen mode Exit fullscreen mode

如此简单而强大也有其代价:最明显的是在循环内部创建闭包。记住闭包可以访问外部函数的数据。
所以在基于`i`的循环中，闭包内的代码将基于`i`的**当前**值执行。不是创建闭包时存在的旧值`i`。这里有一个简单的代码来解释这一点:

```
function arrayOfNums(num) {
  var output = [];
  for (var i = 0; i < num; i++) {
    // Closure being pushed into the output array:
    output.push(function() { return i; }); 
  }
  return output;
}

var arr = arrayOfNums(3);
arr[0](); // Output: 3
arr[1](); // Output: 3
arr[2](); // Output: 3 
```

Enter fullscreen mode Exit fullscreen mode

另一个问题是在超时/间隔内创建闭包。运行时，闭包内的代码将基于外部函数的当前数据执行。该数据的值可能在超时之前就已经过时了。

这里有一个简单的代码来解释这一点:

```
function countdown(upto) {
  for (var i = 0; i < upto; i++) {
    // Closure set to run after 1000ms
    setTimeout(() => console.log(i), 1000);
  }
};

countdown(5); // Output: 5 5 5 5 5 
```

Enter fullscreen mode Exit fullscreen mode

总之，闭包是简单的存在。访问外部函数范围的总是内部函数。如果外部函数被多次调用，每次调用都会创建一个新的闭包。闭包的存在依赖于它们的父函数的存在。就像我说的，简单的生物。