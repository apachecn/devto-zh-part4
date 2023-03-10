# R 中的 JavaScript cont

> 原文：<https://dev.to/colinfay/javascript-cont-in-r-5dpe>

我喜欢 JavaScript 的一点是`const`声明方法，它允许你声明一个变量一次，之后这个变量就不能被重新赋值了。也就是说，这段代码将抛出一个错误:

```
node -e "const x = 12; x = 14"

## [eval]:1
## const x = 12; x = 14
## ^
## 
## TypeError: Assignment to constant variable.
## at [eval]:1:18
## at Script.runInThisContext (vm.js:124:20)
## at Object.runInThisContext (vm.js:314:38)
## at Object.<anonymous> ([eval]-wrapper:9:26)
## at Module._compile (internal/modules/cjs/loader.js:805:30)
## at evalScript (internal/process/execution.js:60:25)
## at internal/main/eval_string.js:16:1 
```

最酷的是你不能错误地覆盖变量:一旦它被设置，它就被设置了。另一方面，R 允许你覆盖几乎任何变量(除了一些保留变量)。

我问 Twitter 在 R 中是否有这个概念的实现。例如，当你有一个值需要一些时间来计算时，用例就会出现。如果我做我的计算，我可能会在以后不小心覆盖它。事件更多如果您使用笔记本，您可以在整个文档中创建符号和值。

```
a  <-  some_very_complex_computation()  # [...] Going on the weekend  a  <-  "Hello there!" 
```

在这里，我没有办法阻止自己擦除`a`中的值。当然，总是有严格性、明确的变量名和不加思考地赋值，但是你知道它在现实世界中是怎样的，那里没有 Cmd + Z。

罗曼指出`?lockBinding`确实存在，那正是我一直在寻找的。确实如此。

它是这样工作的:它接受一个引用一个符号和一个环境的字符串，并阻止在给定的环境中给这个符号赋值。

```
x  <-  12  lockBinding("x",  .GlobalEnv)  x  <-  13  ## Error in eval(expr, envir, enclos): cannot change value of locked binding for 'x' 
```

这里有一个小包装器来做这件事:

```
lock  <-  function(x){  lockBinding(  deparse(  substitute(x)),  env  =  parent.frame()  )  }  plop  <-  12  lock(plop)  plop  <-  13  ## Error in eval(expr, envir, enclos): cannot change value of locked binding for 'plop'  pouet  <-  function(){  plop  <-  14  print(plop)  lock(plop)  plop  <-  13  }  pouet()  ## [1] 14  ## Error in pouet(): cannot change value of locked binding for 'plop' 
```

所以在那里我可以做

```
a  <-  some_very_complex_computation()  lock(a)  # [...] Going on the weekend  a  <-  "Hello there!" 
```

在那里，我阻止了自己删除我的`a`变量。当然，它和 JavaScript `const`不一样，因为总是有办法解锁这个符号。

```
x  <-  12  ## Error in eval(expr, envir, enclos): cannot change value of locked binding for 'x'  lock(x)  x  <-  13  ## Error in eval(expr, envir, enclos): cannot change value of locked binding for 'x'  unlockBinding("x",  .GlobalEnv)  x  <-  13  x  ## [1] 13 
```

但是我认为这是一个防止不必要的变量覆盖的非常好的解决方案。

另请参见:

*   [用 ActiveBinding 制作 R 中的常数](https://iqis.netlify.com/post/2019/07/22/how-to-make-a-constant-in-r/)

Twitter 帖子的一些回答也建议使用 R6…但那将是另一个帖子:)