# 面试准备—基础 JavaScript-1

> 原文：<https://dev.to/nabendu82/interview-preparation-basic-javascript-1-117c>

我们都知道 JavaScript 面试(尤其是前端面试)可能很难，因为面试官可能会问旧 JS、新 ES6 JS、DOM 元素操作、旧 CSS 布局、新 FlexBox 和 CSS 网格布局、算法和复杂性、小编码问题、ReactJS 及其生命周期方法等问题。

现在，面试有许多不同的类型，由于其性质和包含不同类型的问题。这些轮次主要由中小型公司跟进，不适用于科技巨头(FB、谷歌、Flipkart 等)。它们大致分类如下。

*   **第一轮-通常是电话-** 首先介绍一下你自己，关于 var、React 及其生命周期方法的基本老 JS 问题。
*   **第一轮-基于时间的在线问题-** 在 1 小时内回答一组 30 个基本的 JS、CSS 问题，或者在 1 小时内回答 3 个难题。
*   **第一轮- Skype 面试-** 以自我介绍开始，接下来是老 JS 问题和 ReactJS/JavaScript 中的一个小组件或程序。
*   **第一轮-小型创业公司-** 给你一个小的补充，以完成他们现有的网站。
*   **第二轮-面对面面试-** 再次从自我介绍开始，然后是纸上/白板上的简单 JS 问题。
*   **第三轮-与架构师面对面-** 非常棘手的 JavaScript 问题或在白板上解决问题。
*   **第四轮-客户轮-** 仅针对服务公司。大多数小问题都可以通过在 skype 上共享屏幕来解决。
*   **第五轮- HR-** 主要是薪资谈判。

这个系列是基于我接受采访的经验，以及我的许多前端开发者朋友的意见。此外，我已经给出了来自 [techsith](https://www.youtube.com/watch?v=oxoFVqetl1E&list=PL7pEw9n3GkoWn5TcqAdmSzXcvC3d_tfAh) 的惊人采访系列的例子。

让我们从基本的 JavaScript 问题开始，这些问题通常在第一轮中被问到，有时在第二轮中也会被问到。

**问题 1-** *关键字 let、const、var 有什么区别？*
**答案-** 变量 var 从 JS 开始就有了但是变量 let 和 const 是最近在 ES6 中引入的。
var 是*函数作用域*，而 let 和 const 是*块作用域*。
我们先来了解一下 var 和 let 的区别。考虑下面的例子。它有生命，里面有一个假设。if 语句为 let 变量创建了一个作用域，但是 var 没有 block 作用域，所以也可以在它的外部访问它。

```
(function(){
  if(true){
    var v = 10;
    let l =20;    
  }
  console.log('v is ', v);
  console.log('l is ', l);
})() 
```

如果我们运行下面的，我们得到的是 var 的值，而不是 let 的参考误差。

[![reference error for let](img/a61201dc7289b6fceabe911c66cbbac3.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--K-Tl08rt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/5760/1%2AEFqwTgVzhzun7BlLm7-WlA.png) *参考错误让*

我们现在可以理解这个常数了。创建它是为了在 JavaScript 中声明常量变量。对于整数和字符串之类的原始类型，我们不能在初始声明之后给变量赋值，但是可以为数组和对象之类的非原始类型添加值。

让我们看看它的例子。重新分配常量整数值会导致引用错误。

```
(function(){
  const c = 12;
  c = 14;
  console.log('c is ', c);
})()

/*
Exception: TypeError: invalid assignment to const `c'
[@Scratchpad](http://twitter.com/Scratchpad)/1:3:3
[@Scratchpad](http://twitter.com/Scratchpad)/1:1:2
*/ 
```

重新分配常量字符串值会导致引用错误。

```
(function(){
  const s = "coder";
  s= "developer"
  console.log('s is ', s);
})()

/*
Exception: TypeError: invalid assignment to const `s'
[@Scratchpad](http://twitter.com/Scratchpad)/1:9:3
[@Scratchpad](http://twitter.com/Scratchpad)/1:7:2
*/ 
```

重新分配数组会产生错误。

```
(function(){
  const arr = [1, 2, 3];
  arr = [3, 4, 5];  
  console.log('arr is ', arr);
})()

/*
Exception: TypeError: invalid assignment to const `arr'
[@Scratchpad](http://twitter.com/Scratchpad)/1:15:16
[@Scratchpad](http://twitter.com/Scratchpad)/1:13:2
*/ 
```

但是增加数组的值是可能的。

```
(function(){
  const arr = [1, 2, 3];
  arr.push(4);  
  console.log('arr is ', arr);
})()

//output - arr is [1, 2, 3, 4] 
```

并且添加和改变对象中的值也是可能的。

```
(function(){
  const obj = {name: 'Nabendu', skill: 'JS'};
  obj.skill = 'React'; 
  obj.profession= 'Developer';
  console.log('obj is ', obj);
})()

//output is obj is { name: "Nabendu", skill: "React", profession: "Developer" } 
```

**问题 2-***= =和= = = =有什么区别？*
**答-** 两者都是 JavaScript 中的比较运算符。==在比较之前进行类型转换，但是===进行严格比较，不进行类型转换。

考虑下面的==。字符串 1 和数字 1 之间的比较产生了 **true** ，因为数字 1 在比较之前被转换为字符串。

```
if('1' == 1) {
  console.log('They are equal')
}

//Output - They are equal 
```

现在，考虑===的同一个例子。它将产生 false，因为没有进行类型转换。

```
if('1' === 1) {
  console.log('They are equal')
} else {
  console.log('They are not equal')  
}

//Output - They are not equal 
```

**问题 3-***null 和 undefined 有什么区别？*
**答案-** 两者都代表空值。但是不同的是当你定义了一个变量而没有赋值的时候，JS 会给它赋值 undefined。null 通常由用户在想要清除某些值的情况下指定。

```
let a;
console.log(a);

//output - undefined 
```

另一个区别是 **typeof(未定义)是未定义的**但是 **typeof(空)是对象**。它应该为空，但这是早期 JavaScript 中的一个错误。

**问题 3-** *什么是原型继承？*
**答案-** 原型继承意味着，利用原型创造新的能力。让我们考虑下面的例子。这里，我们创建一个汽车构造函数，然后用 new 关键字调用它。现在，nabsCar 和 shikhaCar 变量有自己的 Car 实例，但是可以有一个公共的原型函数 getModel()

```
let Car = function(model) {
  this.model = model;
}

Car.prototype.getModel = function() {
  return this.model;
}

let nabsCar = new Car('maruti800');
console.log(nabsCar.getModel()); //Output - maruti800

let shikhaCar = new Car('hyndaiAccent');
console.log(shikhaCar.getModel()); //Output - hyndaiAccent 
```

**注意**:我有一个关于物体和原型的详细系列。你可以在这里找到[。](https://dev.to/nabendu82/ydkjs-objects-and-prototypes-part1-1jml)

基本 JavaScript 面试问题的第 1 部分到此结束。