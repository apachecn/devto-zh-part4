# 了解 JavaScript 中的括号

> 原文：<https://dev.to/michalbryxi/know-your-brackets-in-javascript-1h07>

JavaScript 中定义和调用函数的基本语法是:

```
function foo() {return '🙀'}; foo(); 
// "🙀" 
```

使用新的 [ES6 胖箭头语法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)你也可以将函数存储在一个变量中，然后像以前一样调用它:

```
foo = () => {return '🙀'}; foo();
// "🙀" 
```

箭头函数有一个选项[简洁体](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions#Function_body)，它有隐式返回。您只需省略花括号和返回语句:

```
foo = () => '🙀'; foo();
// "🙀" 
```

那么如果我们想在一个简洁的主体中返回一个 hash 呢？我的第一个猜测是:

```
foo = () => {cat: '🙀'}; foo();
// undefined 
```

不工作。为什么？原来 JavaScript 有一个标签 T2 的概念。所以`cat`就是这个代码中的*标签*。

要对一个**散列**进行**隐式返回**，必须将散列括在括号中:

```
foo = () => ({cat: '🙀'}); foo();
// {cat: "🙀"} 
```

现在，如果我们想直接调用函数，而不是把它存储在任何地方，我们将不得不再次把整个函数定义放在括号中:

```
(() => ({cat: '🙀'}))();
// {cat: "🙀"} 
```

我们可能还想直接获取`cat`键的值。ES6 有一个很好的[析构赋值语法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)用于此:

```
let {cat} = (() => ({cat: '🙀'}))(); cat
// "🙀" 
```

### 学分

*   米哈伊尔·瓦西里耶夫