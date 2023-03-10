# 编写不可变的函数式 JavaScript

> 原文：<https://dev.to/jacobmgevans/writing-immutable-javascript-why-how-3if6>

# 不变的概念和技术

## 在 JavaScript 和 React 中使用不可变做法的原因:

*   降低了引入细菌的表面积。

    *   原因很简单，输入数据进入函数/方法，产生可预测的输出。可以用来坚持这一点的是纯函数。进一步的例子可以是应用于高阶函数的隐性编程的例子。
*   创建数据和变更的历史记录；可能有用的是跟踪数据流，甚至是调试之类的东西。

    *   利用这些概念的系统的一个例子是 Redux，它处理数据变更的方法创建了一个可以逐步执行的变更历史。

## 纯函数:

*   无副作用；返回数据，不修改其范围之外的任何数据。
*   可预测地返回具有相同输入的相同值(输出)。

因此，只要函数中没有引入 X 因子，输出就是“可预测的”;唯一进入函数(func1)的是输入。如果引入了 API 数据或来自另一个函数(func2)的数据，并且这些数据也根据输入而变化，那么您就不能肯定地说

## 常量赋值:

*   const 在防止重分配和重声明方面很棒。

```
const variable = 'hello world'
try{
variable = 'Hello World!'
}catch(error){
console.log(error) // TypeError: invalid assignment to const 'variable'
} 
```

Enter fullscreen mode Exit fullscreen mode

*   const 不会独自解决这个问题，变量重分配预防是问题的一半，甚至可能不到变异问题的一半...但说到变量数据，肯定是一半。

ECMAScript 提议将一个关键字添加到赋值语句的右侧，以防止数据突变，这是一个很棒的提议。它可能看起来像

```
const obj = immut { a: 1, b: 2 }

obj['a'] = 3 // Error Thrown

console.log(obj['a']) // 1 
```

Enter fullscreen mode Exit fullscreen mode

## 冻结物体

*   浅层冻结对象，防止简单对象的意外突变。

```
 const obj = { 
   zoo: 'animals'
   }
   Object.freeze(obj) 

   obj['zoo'] = 'noAnimals'
   console.log(obj['zoo']) // 'animals' 
```

Enter fullscreen mode Exit fullscreen mode

## 传播算子

*   对对象和数组使用 spread 操作符是一种很好的方法，可以从这些源创建数据的浅层副本，然后将旧数据的副本与新数据一起应用到新对象或数组中。

```
const arr = [ 1, 2, 3, 4 ]

const newArr = [ ...arr, 'newData' ]

console.log(arr) // [ 1, 2, 3, 4 ]
console.log(newArr) // [ 1, 2, 3, 4, 'newData' ] 
```

Enter fullscreen mode Exit fullscreen mode

## 高阶函数(HOF)

*   HOF 是一个伟大的工具，但也坚持不变性的概念。HOF 将接受一个函数并返回一个函数。如果你对 HOF 的进一步阅读感兴趣，我建议[雄辩的 JavaScript 第 5 章](https://eloquentjavascript.net/05_higher_order.html)

下面是一个利用 HOF 行为在代码中进一步坚持不变性概念的例子:

```
const arr = [ 1, 2, 3 ]

const newArr = arr.map(ele => ele * 2)

console.log(newArr) // [ 2, 4, 6 ]
console.log(arr) // [ 1, 2, 3 ] 
```

Enter fullscreen mode Exit fullscreen mode

## 默契编程(免点)

所以无点风格是一个允许抽象的功能概念。现在这可能被过度使用，这可能导致[一些 gotcha 的](https://dev.to/danhomola/point-free-gotchas-in-javascript--3pfi)
或者甚至只是因为缺少参数命名而混淆...它甚至因这种过度使用/误用的“无意义风格”而得名我觉得如果使用得当，它是一个单一函数的参数和实现的抽象。

```
const arr = [ 1, 2, 3 ]

const addTwo = (ele) => ele + 2

const twoAddedPointFree = arr.map(addTwo)

console.log(twoAddedPointFree) // [ 3, 4, 5 ]

// You can even reuse it! 
const fourAdded = twoAddedPointFree.map(addTwo)
console.log(fourAdded) // [ 5, 6, 8 ] 
```

Enter fullscreen mode Exit fullscreen mode