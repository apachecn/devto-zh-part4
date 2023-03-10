# Javascript 变量及其数据类型

> 原文：<https://dev.to/gvinod1991/javascript-variables-and-it-s-data-types-44gl>

## 如何在 javascript 中声明变量？

有 3 种类型的变量声明。

首先使用“var”关键字，这是一个声明变量的老方法。

```
Syntax: - var "Variable Name";
var x;//Now x value is undefined means we not defined the variable yet
var y=5; //Now y value is 5 as we assigned 5 to it 
```

Enter fullscreen mode Exit fullscreen mode

第二，使用“让”关键字

```
let x=5; //Now x value is 5 as we assigned 5 to it 
```

Enter fullscreen mode Exit fullscreen mode

var 和 let 关键字之间的区别是用" let "关键字声明的变量有块范围，而在" var "的情况下有函数范围。

参见下面的代码

```
{
    let i=0;
}
console.log(i)//Throws ReferenceError:i is undefined 
```

Enter fullscreen mode Exit fullscreen mode

在这里，I 的范围仅限于花括号内。花括号外的变量 I 不可用。

```
{
    var x=5;
}

console.log(x);//5 
```

Enter fullscreen mode Exit fullscreen mode

在这里，变量 x 的范围在花括号内和花括号外都是可用的，因为用“var”关键字声明的变量被提升(只有变量声明被移动到函数或程序的顶部)
以上代码执行如下

```
var x;
{
    x=5;
}
console.log(x)//5 
```

Enter fullscreen mode Exit fullscreen mode

第三，使用 const，如果我们使用 const 关键字声明一个变量，那么首先被赋值的值将不会被其他值更新。

```
const x=4;
x=5//Throws TypeError: Assignment to constant variable. 
```

Enter fullscreen mode Exit fullscreen mode

## 数据类型和类型检查

javascript 中可以声明 6 种基本的变量数据类型。

```
1.Number
2.String
3.Boolean
4.Object
5.Undefined
6.Null 
```

Enter fullscreen mode Exit fullscreen mode

ES6 中增加的另一种数据类型是“符号”[我将在以后的博客中介绍这种数据类型]。Javascript 变量是动态类型的，这意味着如果我们声明一个变量并赋值给它，那么它将转换这个值类型。

```
var x=5;
console.log(x)//Type is Number
x="Ryu" // Type is String 
```

Enter fullscreen mode Exit fullscreen mode

我们可以使用 **typeof** 函数来检查变量的类型

```
let x=5;
typeof(x) //Number

let x="Ryu";
typeof(x) //String

let x=true;
typeof(x) //Boolean

let x={};
typeof(x) //Object

let x=[];
typeof(x) //Object (In javascript array is also a object)

let x;
typeof(x) //Undefined

let x=null
typeof(x) //Object(It's a Javascript's exception is that typeof null is also a object) 
```

Enter fullscreen mode Exit fullscreen mode

注意:-这个博客简要介绍了 Javascript 中的变量及其数据类型。关于这个话题有很多概念没有在这个博客中讨论。

在我的(推特)[[https://twitter.com/GodtiVinod](https://twitter.com/GodtiVinod)]上关注我，在(LinkedIn)[[https://www.linkedin.com/in/godti-vinod-37bb46a9/](https://www.linkedin.com/in/godti-vinod-37bb46a9/)上联系我