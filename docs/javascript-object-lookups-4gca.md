# Javascript 对象查找

> 原文：<https://dev.to/devtoncho/javascript-object-lookups-4gca>

原帖在 [toncho.dev](https://toncho.dev/javascript-object-lookups)

* * *

我们将看到对象查找有时如何被用作 if 和 switch 语句的更易维护的替代方法。

* * *

### 开关

[`switch`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/switch) 语句对表达式求值，将值与`case`子句匹配，然后执行相关的语句。switch 所做的是接受输入，并根据`case`子句提供输出。

```
const type = 'apple';

let fruit;

switch(type) {
  case 'apple':
    fruit = 'Apple';
    break;
  case 'pear':
    fruit = 'Pear';
    break;
  default:
    fruit = 'Unknown fruit!';
    break;
}

console.log(fruit);
// expected output: "Apple" 
```

`switch`语句有很多问题，它有非标准的方法来处理代码块，JavaScript 的其余部分使用花括号，但是 switch 没有。我们被迫在每个用例中添加`break`语句，这可能导致调试困难，在编写每个`case`子句时出错，因为经常忘记添加这个`break`语句，而且`case`子句的数量增加，对象的性能变得比用例顺序重要的交换机的平均成本更好。对象方法是散列表查找，交换机必须评估每种情况，直到找到匹配和断点。

* * *

### 如果和否则

[`if`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/if...else) 和 [`else`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/if...else) 语句类似于`switch`，但是它计算单个值，如果指定的条件为真，则`if`语句执行，否则如果条件为假，则`else`语句执行，当它们是大量的`else if`语句时，某些东西确实是错误的，通常您应该使用类似于`switch`的东西，因为它更适合目的和意图。

```
const getFruit = (type) => {
  if (type === 'apple') {
    type = 'Apple';
  } else if (type === 'pear') {
    type = 'Pear';
  } else {
    type = 'Unknown fruit!';
  }

  return type;
}

const type = 'apple';

console.log(getFruit(type));
// expected output: "Apple" 
```

* * *

### 对象查找

我们一直在使用对象，要么作为构造函数，要么作为文字。我们将从对象属性中获取值。

```
const getFruit = (type) => {
  return {
    'apple': 'Apple',
    'pear': 'Pear',
  }[type];
}

const type = 'apple';

console.log(getFruit(type));
// expected output: "Apple" 
```

这个例子是将一个值映射到另一个值。通过对象查找，我们提供了一种更简洁的方式来编写代码，我们不必担心`case`子句或`break`语句，并且更容易阅读和快速理解正在发生的事情。