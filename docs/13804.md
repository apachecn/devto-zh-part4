# 理解 JS 三元运算符

> 原文：<https://dev.to/kiarathedev/understanding-js-ternary-operators-5epi>

作为一个开发初学者，看到这样的代码可能有点，嗯，不知所措。

`let person = {
name: 'John',
age: 25,
legal: null
};`

`person.legal = person.age >= 21 ? 'Yes' : 'No';`

[![overwhelmed](img/2b18f526a4d3393ca0b02f598797998b.png)](https://i.giphy.com/media/oubM1tKqnLW5G/giphy.gif)

不要担心！条件(三元)操作符可以很容易使用，并将简化您的代码，即使只是一点点。

### 三元运算符代表什么？

三元运算符用作`if else`语句的简写。`if else`语句检查一个条件，通常有以下语法:

`if (condition) {
value if true;
} else {
value if false;
}`

所以，如果我想确定上面的人是否超过了美国法定饮酒年龄，我可以写一个这样的声明。

`if (person.age >= 21) {
person.legal = 'Yes';
else {
person.legal = 'No';
}`

在上面的例子中，我能够确定这个人是否合法，并给`person.legal`赋值。但是我们也可以通过使用三元运算符来实现这一点！下面是语法:

`condition ? value if true : value if false`

让我们返回给出的第一个例子，而不是使用一个`if else`语句。

`person.legal = person.age >= 21 ? 'Yes' : 'No'`

在这里，我通过检查他们的年龄来决定`person.legal`是或不是。如果`person.age >= 21`则返回 Yes。否则`person.legal`设置为否

### 事情知道了

*   条件是被测试的内容，无论是一个`if else`语句还是三元运算符。
*   一个`?`将条件与真实值分开。如果条件为真，则执行在`?`之后和`:`之前的任何内容。
*   如果条件评估为 false，则执行冒号后的任何代码。

总之，我感谢你阅读这篇文章，并希望你现在可以使用三元运算符！除了经验之外，我的大部分信息来自于[这篇精彩的文章](https://codeburst.io/javascript-the-conditional-ternary-operator-explained-cac7218beeff)，它继续解释高级三元运算符，比如嵌套三元和多重三元。