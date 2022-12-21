# 条件条件下的代码复制重构

> 原文：<https://dev.to/jhynzar/code-duplication-refactoring-on-conditional-conditions-3h10>

有时你会遇到重复的代码块，唯一的区别是有一个额外的条件来检查另一个条件是否为真。下面给出了这个想法的一个例子。

## 从

```
if ( condition1 && condition2 ) {
//doSomething1

    if ( conditionA || conditionB ) {
        //doSomething2
    }

} else if ( condition1 ) {
//doSomething1

    if ( conditionA ) {
        //doSomething2
    }

} 
```

在上面的例子中，唯一的区别是如果`condition2 == true`然后检查`conditionB`
来重构它，我们把所有的执行权留给了`condition2`和`conditionB`

> 假设:
> 
> `condition1 = true`
> 
> `conditionA = false`

这样，我们得到:

```
if ( true && condition2 ) {
//doSomething1

    if ( false || conditionB ) {
        //doSomething2
    }

} else if ( true ) {
//doSomething1

    if ( false ) {
        //doSomething2
    }

} 
```

> 在上面第一个`if`块的例子中，当且仅当`condition2`和`conditionB`是`true`时，你才执行`doSomething2`。
> 所以我们可以将其重构为下面的代码。

## 到

```
if ( condition1 ) {
//doSomething1

    if ( conditionA || (condition2 && conditionB) ) {
        //doSomething2
    }

} 
```

Codepen 示例:[https://codepen.io/jhynzar/pen/QRJpob?editors=0012](https://codepen.io/jhynzar/pen/QRJpob?editors=0012)