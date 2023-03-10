# 最被低估的 Javascript 方法之一

> 原文：<https://dev.to/jaydeshmukh/underrated-javascript-method-551l>

`some()`返回一个布尔值。它检查数组中是否至少有一个元素通过了提供给回调函数的测试条件。

现在让我们假设你有一系列的菜肴

```
const buffet = [
 { dish: 'butter chicken' , type: 'non-veg' },
 { dish: 'mutton curry' , type: 'non-veg' },
 { dish: 'meat balls' , type: 'non-veg' },
 { dish: 'butter paneer' , type: 'veg' },
 { dish: 'mutton curry' , type: 'non-veg' },
 { dish: 'meat balls' , type: 'non-veg' },
 { dish: 'mutton curry' , type: 'non-veg' },
 { dish: 'meat balls' , type: 'non-veg' },
 { dish: 'butter paneer' , type: 'veg' },
] 
```

现在，假设您想检查自助餐是否有蔬菜。大多数时候我看到人们做类似的事情(包括我...直到我偶然发现了这个方法)

```
let containsVegItem;
buffet.forEach((dish) => {
    if (dish.type === 'veg') {
     containsVegItem = true;
         return
    }
}) 
```

这可以通过使用`some()`的一行代码简单地完成，这也提供了额外的性能优势

```
buffet.some((dish) => dish.type === 'veg') 
```

以下是一些性能指标评测

```
 console.time('P1');
let containsVegItem;
buffet.forEach((dish) => {
    if (dish.type === 'veg') {
     containsVegItem = true;
         return
    }
})
console.timeEnd('P1') 
```

`>>> P1: 0.052978515625ms`

```
 console.time('P2');
buffet.some((dish) => dish.type === 'veg')
console.timeEnd('P2'); 
```

`P2: 0.0400390625ms`

感谢你到目前为止阅读这篇文章。不，根本没有晋升🤣 ...如果你觉得有用，请随意点击...如果你害羞，不想这样做，也没关系😂

参考资料:-

[https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Global _ Objects/Array/some](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/some)