# 去做异步函数吧，这比看起来容易

> 原文：<https://dev.to/mmatheuspalma/go-make-async-use-cases-for-async-functions-3lbn>

有了 ES5 的新特性，我们可以快速构建异步函数；

让我们编码

首先，我们建立一个承诺，这个承诺将检查数字是否是奇数

```
const isOdd = (value) => {
  return new Promise((resolve, reject) => {
    const isOdd = value % 2 !== 0;

    (isOdd)
      ? resolve(`the number ${value} is odd`)
      : reject(`the number ${value} is even`);
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

“承诺”将被执行，我们需要得到“回应”

```
const allNumbersAreOdd = (number1, number2) => {
  isOdd(number1).then(response => {
    isOdd(number2).then(response => {
      console.log('all numbers are odd');
    }).catch(error => {
      console.log(error);
    });
  }).catch(error => {
    console.log(error);
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

因此维护可能会很困难，这取决于代码的大小

```
allNumbersAreOdd(1, 2); // "the number 2 is even"
allNumbersAreOdd(1, 1); // "all numbers are odd" 
```

Enter fullscreen mode Exit fullscreen mode

带异步功能

```
async const numberIsOdd = (number) => {
  try {
    const numberIsOdd = await isOdd(number);

    console.log(numberIsOdd);
  } catch (error) {
    console.log(error);
  }  
} 
```

Enter fullscreen mode Exit fullscreen mode

```
numberIsOdd(5) // "the number 5 is odd" 
```

Enter fullscreen mode Exit fullscreen mode

如果我们需要调用多个承诺，与传统方式相比
更容易维护，因为我们可以在同一个函数
中处理错误

```
async const allNumbersAreOdd = (number1, number2) => {
  try {
    await isOdd(number1);
    await isOdd(number2);

    console.log('all numbers are odd');
  } catch (error) {
    console.log(error);
  }  
} 
```

Enter fullscreen mode Exit fullscreen mode

```
allNumbersAreOdd(1, 2); // "the number 2 is even"
allNumbersAreOdd(1, 1); // "all numbers are odd" 
```

Enter fullscreen mode Exit fullscreen mode