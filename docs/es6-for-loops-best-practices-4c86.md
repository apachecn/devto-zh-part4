# ES6 for 循环:最佳实践

> 原文：<https://dev.to/godcrampy/es6-for-loops-best-practices-4c86>

For 循环是常见的控制流语句，用于迭代范围、顺序数据类型等。JavaScript 的 ES6 修订版提供了几个新特性，使得该语言更加强大、简洁和优雅。以下是我遇到的关于 *for* 循环的最佳实践:

## 香草`for`循环

```
// Old and Bad way

for(var i = 0; i < 10; i++) {
  // Do Stuff
}
// End of loop

console.log(i)
// 10
// iterator still accessible after the end of the loop 
```

Enter fullscreen mode Exit fullscreen mode

在传统的 *for* 循环中，使用 *var* 来初始化迭代器，使得迭代器即使在循环结束后也是可访问的。一个更好的选择是使用更新的 *let* 关键字来初始化迭代器。

```
// Better way

for(let i = 0; i < 10; i++) {
  // Do Stuff
}
// End of loop

console.log(i)
// undefined
// iterator not accessible 
```

Enter fullscreen mode Exit fullscreen mode

*let* 关键字将迭代器的范围限制在 for 循环块。

## 较新口味的`for`循环

ES6 修订版还提供了两个新的 for 循环*..的*和*为..在*循环中。

***为..* :**

```
let primes = [2, 3, 5, 7];

for(const value of primes) {
  console.log(value);
}
// 2
// 3
// 5
// 7
// Iterates over the values of the array 
```

Enter fullscreen mode Exit fullscreen mode

***为..在* :**

```
let primes = [2, 3, 5, 7];

for(const key in primes) {
  console.log(key);
}
// '0'
// '1'
// '2'
// '3'
// Iterates over the keys of the array 
```

Enter fullscreen mode Exit fullscreen mode

注意，*为..在*循环中，这里以字符串而不是数字的形式返回密钥。*的另一件怪事为..在*循环中，当*为..*循环不能:

```
let sandwich = {
  grilled: true,
  butter: "lots",
  bread: "whole wheat",
  calories: 250
}

for(const value of sandwich) {
  console.log(value)
}
// Error: Objects are not iterable

for(const key in sandwich) {
  console.log(key)
}
// "grilled"
// "butter"
// "bread"
// "calories" 
```

Enter fullscreen mode Exit fullscreen mode

## `const` vs `let`

如果你真的仔细阅读，你会注意到我在普通的*中使用了 *let* 用于*循环，在 ES6 风格的*中使用了 *const* 用于*循环。的普通*只是增加了初始迭代器值，整个循环只有一个作用域。因此使用 *const* 不起作用，因为在下一次迭代中增加迭代器值会导致错误。然而在较新的循环中，每次迭代都会创建一个新的作用域。因此，我们可以在这些循环中使用 *const* 以及 *let* 。在我们不想改变 iterable 的值的情况下，const* 更受欢迎。

> 就是这样，伙计们！谢谢你的阅读，祝你有美好的一天😄