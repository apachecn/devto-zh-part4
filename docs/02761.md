# ES6:默认、休息和传播。

> 原文：<https://dev.to/epicosity/es6-default-rest-and-spread-36ce>

ES6 增加了语法糖来帮助我们编写更干净、更简洁的代码。今天，我将讨论这个特性:默认参数、rest 参数和 spread 运算符。

### 默认

在 JavaScript 中，如果没有值被传递到函数中，参数将默认为未定义。将函数参数设置为 undefined 的默认行为会导致函数出错，这就是默认参数发挥作用的地方。

当不传递任何值或参数值未定义时，如果需要用值初始化命名参数，则使用默认函数参数。让我们看一个例子，什么时候你可能想使用默认的函数参数。

下面是一个通用增量函数的例子。该函数接收两个值，并将它们相加。第一个参数是初始数字，第二个参数是我们需要给初始数字增加多少。

```
function increment(number, increment){
  number + increment;
}

increment(2, 5); // 7 
```

Enter fullscreen mode Exit fullscreen mode

让我们用默认的函数参数使我们的函数更灵活一点。假设您希望，如果用户没有为 increment parameter 输入第二个参数，该函数仍会运行并将初始数字递增 1。让我们用 ES5 语法来试试:

```
function increment(number, increment){
  if (typeof increment == 'undefined' ) increment = 1;
  return number + increment;
} 

increment(1, 2) // 3
increment(1) // 2 
```

Enter fullscreen mode Exit fullscreen mode

我们编写了一个条件来检查 increment 参数是否未定义，如果未定义，我们就给 increment 参数赋值 1。

使用 ES6 语法，您可以检查未定义的命名参数，而不需要使用 ES5 所需要的条件。下面是:

```
function increment(number, increment = 1){
  return number + increment;
} 

increment(5,5); // 10
increment(5); // 6 
```

Enter fullscreen mode Exit fullscreen mode

如果没有提供值，您可以在希望重新赋值的参数后添加一个等号，而不是在函数体中编写条件。现在，不仅您的代码更加灵活，而且您有了一个更简洁的函数，更容易阅读，更不容易出错。

### 休息和传播

rest 和 spread 运算符看起来相同，但执行不同的操作。两者都使用三个点...语法 but rest 用于浓缩，spread 用于展开。

### 休息

rest 运算符用于将一组元素压缩到一个数组中。

让我们使用 reduce array 辅助函数创建一个将三个数相加的函数。

如果你需要复习 Array.reduce 是如何工作的，可以看看我上一篇关于 [Array Helper 方法](https://dev.to/epicosity/javascript-array-helper-methods-40mp)的博文。

```
function add(x, y, z){
  let numbers = [x, y, z];

  return numbers.reduce((acc, number) => {
    return acc + number;
  }, 0);
}

add(1, 2, 3); // 6 
```

Enter fullscreen mode Exit fullscreen mode

当参数数量有限时，上面的函数工作得很好，在这种情况下，只有三个数字可以加在一起。如果你传递第四个或第五个参数会发生什么？

```
add(1, 2, 3, 4, 5); // 6 
```

Enter fullscreen mode Exit fullscreen mode

如上所述，该函数将只接受传递给它的前三个参数，而忽略其余的参数。所以即使传入了额外的参数，结果仍然是 6。

使用 ES6 rest，我们可以使功能更加灵活。现在，有了 rest，add 函数可以接受数量不定的参数并返回一个结果。

```
function add(...numbers){
  return numbers.reduce((acc, number) => {
    return acc + number;
  }, 0);
}

add(1, 2, 3, 4, 5); // 15

add(2, 4, 6, 8, 10); // 30 
```

Enter fullscreen mode Exit fullscreen mode

现在，您可以根据需要使用任意多个参数来运行 add 函数。

当你使用...numbers 创建了一个名为 numbers 的新数组，传递给 add()函数的所有参数都将被推入该数组。将所有参数读入数字数组后，函数将继续运行。在数字数组上减少。

### 传播开来

rest 操作符用于将元素压缩到一个数组中，而 spread 操作符用于相反的操作。Spread 用于将一个 iterable(如字符串或数组)转换成它的单个元素。

让我们通过使用 spread 连接数组来看看它是如何工作的。在使用 ES6 扩展语法重写之前，我们先来看看它在 ES5 中是如何实现的。

```
// ES5 array concatenation

const array1 = ['Cersie', 'Jaime'];
const array2 = ['Tyrion'];

const combinedArray = array1.concat(array2);

console.log(combinedArray); // ['Cersie' 'Jaime', 'Tyrion'] 
```

Enter fullscreen mode Exit fullscreen mode

在 ES5 中，我们必须创建一个新的数组，并在一个数组上运行 Array.prototype.concat()方法，并将另一个数组作为参数传入。

使用 ES6 spread，您只需通过放置...在两个数组前面的`[]`括号内连接数组。

```
// Array concatenation with spread
const array1 = ['Cersie', 'Jamie'];
const array2 = ['Tyrion'];

const lannisters = [...array1, ...array2];

console.log(lannisters); // ['Cersie' 'Jaime', 'Tyrion'] 
```

Enter fullscreen mode Exit fullscreen mode

当你看到...在 JavaScript 代码中，您可以通过这些通用规则来区分 rest 和 spread。

1.  如果语法用于函数声明或表达式的函数参数中，那么它就是 Rest 参数语法。
2.  否则，它是扩展运算符。

* * *