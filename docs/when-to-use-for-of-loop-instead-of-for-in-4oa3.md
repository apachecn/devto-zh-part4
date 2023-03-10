# 何时用于..of 循环而不是 for..在

> 原文：<https://dev.to/umaralam48/when-to-use-for-of-loop-instead-of-for-in-4oa3>

传统的 for 循环已经成为过去，因为 ES6 带来了漂亮简洁的循环格式。但是了解这两种语法之间的区别以及它们如何处理不同的数据类型是很重要的。

`for..in`和`for..of`的主要区别在于`for..in`迭代对象的可枚举属性，而`for..of`迭代 iterable 定义要迭代的值。这个一会儿就清楚了。但是这如何影响使用一个而不是另一个呢？

```
var fares = [0.3, 0.5, 0.7, 1, 1.3] 
for(let fare in fares){     
    console.log(fare);  //0,1,2,3,4 
} 
```

我们可能期望控制台中有数组值，但却看到了索引。这是因为在 Javascript 中，数组也是以索引为属性的对象。由于`for..in`在可枚举属性上循环，因此您会看到那里的索引。

现在，如何循环数组中的值呢？这里是`for..of`循环发挥作用的地方。

```
var fares = [0.3, 0.5, 0.7, 1, 1.3] 
for(let fare of fares){     
    console.log(fare);  //0.3,0.5,0.7,1,1.3 
} 
```

这工作得很好，我们在控制台中获得了值！让我们再看一遍这两者，但这次使用对象。

```
var cars={
    one: "car1",
    two: "car2",
    three: "car3"
    }; 

for(let car in cars){       
    console.log(car);  //"one","two","three" 
} 
```

正如所料，`for..in`循环遍历我们在控制台中记录的对象的可枚举属性。但是当我们对一个对象使用`for..of`时会发生什么呢？

```
var cars={
    one: "car1",
    two: "car2",
    three: "car3"
    }; 

for(let car of cars){       
    console.log(car);  //Uncaught TypeError: cars is not iterable 
} 
```

哎呀！这样做我们会得到一个 TypeError，这是因为`for..of`循环要求第二个参数是可迭代的。因为，Javascript 中的对象是不可迭代的，除非它们实现了[可迭代协议](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols#The_iterable_protocol)，因此我们得到了这个错误。

现在你知道在哪里使用哪个 for 循环了。要点是对象用`for..in`，数组用`for..of`。

这是我在 **dev.to** 上的第一篇帖子，我很想听听你的想法。我刚刚开始写博客，感谢您的指导和建议，让体验更好。感谢阅读！