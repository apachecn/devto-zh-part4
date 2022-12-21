# 当一些或所有事情都很重要的时候。

> 原文：<https://dev.to/bishal0622/when-some-or-every-things-matters-4edo>

自从箭头操作符(= >)出现在 javascript 中以来，高阶函数(HOF)在 javascript 开发人员中广受欢迎。forEach()、filter()、map()、reduce()等 HOF 操作是开发者中最常见的 HOF。这些 HOF 在开发人员中非常受欢迎，以至于它们也适用于许多其他编程语言。

但是你知道 ECMA 在 2015 年发布的其他功能仍然很少使用，比如 every()和 some()，这些功能非常有用。

最近我遇到了一个问题，如果在数组(条件)🧐.中有一个大于 20 的值，我必须返回真值

乍一看，我以为我需要一面旗帜🏁它的默认值为 FALSE。然后，在 array 上运行循环，检查每个值的条件，当条件匹配时中断循环。

```
let data = [ 1, 2, 3, 4, 5, 6, 22 ]
let flag = false

for(let i = 0; i <= data.length; i++){
    if(data[i] > 20){
        flag = true
        break
    }
}

if(flag){
    console.log('Above array contains numbers greater than 20.')
}else{
    console.log('Above array does not contain numbers greater than 20.')
}

\\********result**********
\\Above array contains numbers greater than 20. 
```

它是可读的，但我想如果我能使代码更小，如果在‘loadash’中已经有一个这样的函数会怎么样(它已经在我的项目中了)。但是在研究这些函数时，我发现这些很酷的函数 some()和 every()很久以前就已经作为数组操作存在了。

于是，上面的代码变成了:

```
let data = [1,2,3,4,5,6,22]
let flag = data.some( value => value > 20)

if(flag){
    console.log('Above array contains numbers greater than 20.')
}else{
    console.log('Above array does not contain numbers greater than 20.')
}

\\******** Result **********
\\Above array contains numbers greater than 20. 
```

呜啊呜啊...它让我大吃一惊🤯🤯🤯🤯

然后还有另一个功能类似的函数，every()。当 some()检查数组中的任何一个值以满足条件时，every()检查数组中的所有值以满足条件。

```
let data = [ 22, 23, 3, 4, 5, 6]
let flag = data.every( value => value > 20)

if(flag){
    console.log('All the numbers in above array are greater than 20.')
}else{
    console.log('Not all the numbers in above array are greater than 20.')
}

//******* Result ***********
//Not all the numbers in above array are greater than 20. 
```

这里有一些引用[一些()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/some)和[每()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/every)

今天就到这里，你觉得这篇博文有帮助吗？🤞

这是我在网上的第一篇文章🤓