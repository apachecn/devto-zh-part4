# 如果一个操作依赖于另一个第一变量，我该怎么办

> 原文：<https://dev.to/fselcukcan/what-should-i-do-if-an-operation-depends-on-another-first-variable-3ho8>

如果一个操作依赖于另一个第一变量，而这个第一变量是由一个`useEffect`中的 setVariable(关于 react 钩子)设置的，我该怎么办？

该操作可以用第一变量设置另一个第二变量。

我现在使用另一个效果，检查第一个变量是否不像
一样是`undefined`

```
useEffect(() => {
    if (firstVariable) {
        let secondVariable = f(firstVariable)
    }
}, [firstVariable]) 
```

是模式不好吗？有更好的方法吗？

注意:`f`是在别处定义的假设函数。这表明我并没有重命名`firstVariable`,而是对它进行任意操作。什么是操作与本主题无关。