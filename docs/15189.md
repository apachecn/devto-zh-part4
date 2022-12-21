# 箭头函数不像其他函数那样有局部变量参数。

> 原文：<https://dev.to/mckabue/arrow-functions-don-t-have-the-local-variable-arguments-as-do-other-functions-52ba>

箭头函数没有局部变量`arguments`。

```
const arrowFunction = () => { return [...arguments]; }

arrowFunction(1, 2, 3) => ReferenceError: arguments is not defined 
```

普通函数有局部变量`arguments`。

```
const normalFunction = function () { return [...arguments]; }

normalFunction(1, 2, 3) => Array(3) [ 1, 2, 3 ] 
```

但是，您仍然可以在箭头函数中获取参数。

```
const arrowFunction = (...args) => { return args; }

arrowFunction(1, 2, 3) => Array(3) [ 1, 2, 3 ] 
```