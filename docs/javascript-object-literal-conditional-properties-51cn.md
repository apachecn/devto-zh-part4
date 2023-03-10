# Javascript 对象文字条件属性

> 原文：<https://dev.to/devtoncho/javascript-object-literal-conditional-properties-51cn>

原帖在 [toncho.dev](https://toncho.dev/javascript-object-literal-conditional-properties)

* * *

我们经常会遇到向一个`Object`添加条件属性，但是总是以
结束

```
const a = {};
if (someCondition) {
  a.b = 5;
} 
```

为了以优雅的方式处理这个问题，我们将使用 [`spread operator`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax) 和 [`short-circuit evaluation`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_Operators#Short-circuit_evaluation) ，以及 ES6 为`Object`带来的增强功能。

```
const a = {
  ...someCondition && { b: 1 },
} 
```

在 JavaScript 中，使用`&&`和`||`运算符的表达式会被检查是否有可能使用 [`short-circuit evaluation`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_Operators#Short-circuit_evaluation) ，`&&`运算符，只有当两个条件都为真时，这些运算符才会被求值为真。如果第一个条件为假，那么第二个条件甚至不会被计算。只有当两个条件都为假时，`||`操作符才会计算为假。如果第一个条件是真的，第二个条件就不会被评估。此外，`&&`和`||`操作符实际上返回在语句中被求值的最后一个表达式的值。

现在，如果`someCondition`为 true，将返回`{ b: 1 }`和 false，如果`someCondition`为 false，返回值将被传播到对象中，因此，如果表达式返回 false，则不会传播任何内容，也不会向对象添加新的键。