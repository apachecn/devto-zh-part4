# 简化归约累加器功能代码

> 原文：<https://dev.to/pvamshi/simplify-reduce-accumulator-function-code-1ip>

### 问题:

考虑一个非常简单的 reducer 例子:求一个数组中所有元素的和。

```
 const sum = [1, 2, 3, 4, 5].reduce((total, currentValue) => {
    total += currentValue;
    return total;
  }, 0); 
```

或者

```
 const obj = [['a', 4], ['b', 3], ['c', 6]].reduce(
    (accumulator, currentValue) => {
      accumulator[currentValue[0]] = currentValue[1];
      return accumulator;
    },
    {}
  ); 
```

*(这些只是简单的例子为了便于解释，还有更简单的写法，比如:第二个可以换成`Object.fromEntries` )*

我的代码库几乎总是有单行函数表达式，除了 reduce 函数。我从未喜欢过它，直到我找到了用单行书写的方法

### 方案一:传播算子

第二个代码片段可以使用 spread :
进行重构

```
 const obj = [['a', 4], ['b', 3], ['c', 6]].reduce(
    (accumulator, currentValue) => ({
      ...accumulator,
      [currentValue[0]]: currentValue[1]
    }),
    {}
  ); 
```

但是这可能会影响性能，因此不建议这样做。

### 方案二:逗号运算符

在阅读 Angus Croll 的博客[JavaScript 逗号运算符](https://javascriptweblog.wordpress.com/2011/04/04/the-javascript-comma-operator/)时，我遇到了这个奇妙的逗号运算符。

逗号运算符可用于在一行中写多个表达式。以上代码片段可以重构为:

```
 const sum = [1, 2, 3, 4, 5].reduce(
    (total, currentValue) => ((total += currentValue), total),
    0
  ); 
```

```
 const obj = [['a', 4], ['b', 3], ['c', 6]].reduce(
    (accumulator, currentValue) => (
      (accumulator[currentValue[0]] = currentValue[1]), accumulator
    ),
    {}
  ); 
```

其工作原理是`(expr1 , expr2)`从左到右计算每个表达式，并返回最后一个表达式值。

*附注:这纯粹是为了展示逗号运算符的美丽，并且仅出于审美目的，没有任何实际的性能价值(IMHO)。事实上，原始代码应该是首选，因为它可读性更好*