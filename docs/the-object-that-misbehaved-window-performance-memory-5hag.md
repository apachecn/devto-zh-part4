# 行为不当的对象— window.performance.memory

> 原文：<https://dev.to/oryanmoshe/the-object-that-misbehaved-window-performance-memory-5hag>

# 我如何解决没有意义的问题

[![](img/7951e67c9c51ced87d1978c2c9fd53fc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RufN3D7M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o5knc5kj2ovoxce7eaia.png)

今天，我们希望开始在我们的登录页面上跟踪一些新的性能指标，更具体地说，是内存使用情况。
像往常一样，我们从我们信任的`window.performance`对象(从现在开始将被称为*“敌人”*)那里获得数据，在发送之前将其字符串化，然后…

令人惊讶的是，我们得到的只有`"{}"`！

怎么会呢？我们有一个对象，有我们可以访问的属性，但是字符串化它会返回一个空对象！

* * *

### 顺着兔子洞我走

我开始深入研究它，目标是将*敌人*转换成一个字符串，而不用硬编码属性名。

##### 我试着传播它

```
 const result = { ...window.performance.memory };
  console.log(result); // {} 
```

##### 我试着得到它的组成部分

```
 const keys = Object.keys(window.performance.memory);
  const values = Object.values(window.performance.memory);
  console.log(keys); // []
  console.log(values); // [] 
```

##### 我试图破坏它

```
 const { jsHeapSizeLimit } = window.performance.memory;
  console.log(jsHeapSizeLimit); // 2330000000 
```

令人惊讶的是——它成功了！

我非常有信心能够破解这个问题，于是我尝试了“rest”析构:

```
 const { ...rest } = window.performance.memory;
  console.log(rest); // {} 
```

这是怎么回事？为什么这个特定对象拒绝示好？

* * *

### 隧道尽头的一盏灯(那原来是一列火车)

经过更多的修补，我发现另一件事，没有意义。

```
 for (const prop in window.performance.memory)
    console.log(`${prop}: ${window.performance.memory[prop]}`)
  // totalJSHeapSize: 13400000
  // usedJSHeapSize: 12700000
  // jsHeapSizeLimit: 2330000000 
```

迭代敌人的属性确实可以，但是获取它的属性名却不行？甚至 Object.getOwnPropertyNames 也失败了！

尽管这种解决方案对我来说可行(记住，我最初的目标是“将敌人转化为一个字符串，而不需要对属性名进行硬编码”)，但我想找到一种更优雅的解决方案，并想搞清楚这一点。

* * *

### 第一种解决方案，又名“不够好”

我旅行的下一部分是当我开始摆弄敌人的*原型时，它被称为`MemoryInfo`。我试着改变原型，将敌人*分配给一个具有不同原型的新物体，从敌人那里创建一个阵列，并最终结合上述所有技术进行试验。

```
 Object.getOwnPropertyNames(window.performance.memory); // []
  Object.getOwnPropertyNames(window.performance.memory.__proto__) // 
  ["totalJSHeapSize", "usedJSHeapSize", "jsHeapSizeLimit", "constructor"] 
```

成功！嗯……算是吧。我得到了原型属性名，我可以使用这个数组通过下面的代码片段
得到我想要的东西

```
 const success = JSON.stringify(Object.getOwnPropertyNames(window.performance.memory.__proto__).reduce((acc,key) => {
       if (key !== 'constructor')
         acc[key] = window.performance.memory[key];
       return acc;
    }, {})
  );

  console.log(success) // " {"totalJSHeapSize":20500000,"usedJSHeapSize":18200000,"jsHeapSizeLimit":2330000000}" 
```

###### 

<center>*不伟大，不可怕。*</center>

它确实有效(这是我喜欢的一行程序)，不要误会我的意思，但它并不像我希望的那样优雅，而且没有任何意义。

* * *

### 我与代码合一

快进 1 小时，我发现对象有属性，这些属性有元数据(称为描述符),这些元数据定义了我们是否可以枚举属性、更改属性，以及如何从对象中获取属性。

因此，这些属性一定有某种元数据标记，阻止以传统方式获取它们。从 MDN 我发现`enumerable`是我感兴趣的资产:

> 当且仅当该属性在相应对象的属性枚举期间出现。

```
 Object.getOwnPropertyDescriptors(window.performance.memory.__proto__);
// {..., jsHeapSizeLimit: {get: ƒ, set: undefined, enumerable: true, configurable: true}, ...} 
```

但是属性确实打开了`enumerable`元数据属性，为什么在使用`Object.keys`或`Object.values`时它们没有显示出来？

* * *

### 不太隆重的大结局

最后，我成功地“打开”了敌人的*上的可计数标志，我的数据被漂亮地串成了一串* 

```
 const finale = JSON.stringify(Object.defineProperties(window.performance.memory, Object.getOwnPropertyDescriptors(window.performance.memory.__proto__)));
  console.log(finale); // "{"totalJSHeapSize":29400000,"usedJSHeapSize":23100000,"jsHeapSizeLimit":2330000000}" 
```

###### 

<center>*甜蜜的俏皮话*</center>

这是怎么回事？

我们已经看到了`MemoryInfo`原型的描述符应该是没问题的，所以唯一的解释是，不知何故它们没有被设置(或覆盖)在敌人本身的*上。*

使用`Object.defineProperties`我可以得到敌人的*的副本，但是有正确的描述符(我们可以使用`Object.getOwnPropertyDescriptors`从原型中得到)*

那就用绳子绑起来，然后装船！

[![](img/141d185750ab120b01332c41b6a213b9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Iue7kdoP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zbc1zinz2o233pfvpaou.png)