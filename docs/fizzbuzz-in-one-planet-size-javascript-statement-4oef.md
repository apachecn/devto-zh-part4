# 一个星球大小的 JavaScript 语句中的 FizzBuzz

> 原文：<https://dev.to/sergeis/fizzbuzz-in-one-planet-size-javascript-statement-4oef>

JavaScript 有许多诱人的特性，很容易让人忘乎所以。所以我做到了。这是一个 FizzBuzz 一行程序，它使用了 ES5 和 ES6 的全部功能:

*   数组. from()
*   类似数组的对象
*   Array.assign()
*   地图
*   为每一个
*   三元运算符
*   真值和假值
*   生活
*   当然，还有粗箭头函数

```
(n=>Array.from({length:n}, (_,i)=>i+1)
.map(e=>({text:e+' ', value: e}))
.map(e=> e.value%3? e: Object.assign(e, {text:e.text+'Fizz'}))
.map(e=> e.value%5? e: {value:e.value, text:e.text+'Buzz'})
.forEach(e=>console.log(e.text)))(100) 
```

与一个纯过程化的类似 C 的版本相比:

```
for (var i = 1; i <= 100; i++) 
  console.log(i+' '+(i%15==0?"FizzBuzz":i%3==0?"Fizz":i%5==0?"Buzz":"")); 
```

它占用的资源更少，运行速度可能快十倍。

教训？有时候，最好避免那些虚饰，坚持最基本的东西。