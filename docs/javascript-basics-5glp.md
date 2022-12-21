# JavaScript 基础知识

> 原文：<https://dev.to/thefinnomenon/javascript-basics-5glp>

# Javascript 是什么？

JavaScript 是一种高级的动态类型语言，最初是作为一种向 HTML 页面添加功能的方式出现的，现在它几乎出现在所有领域，从后端的数据库和服务器到前端的浏览器、移动设备，甚至电视。

# 重要概念

## 功能

在 Javascript 中有两种方法来定义函数，

```
function foo(param) {
  doSomethingAmazing(param);
} 

foo("bar"); 
```

和(更简洁地)作为箭头功能

```
const foo = (param) => doSomethingAmazing(param);

// If only one param, you can drop the parenthesis
const foo = param => doSomethingAmazing(param);

foo("bar"); 
```

> 箭头函数在调用时从上下文继承 this 对象。

函数也可以有默认参数，如果传入参数，这些参数将被覆盖。

```
function foo(param = 0) {
  ...
}

const foo = (param = 0) => ... 
```

## 异步

### 回调

在 JavaScript 中进行异步调用(例如从数据库中检索项目)的最初方法是使用回调。回调函数是作为参数(通常是最后一个)传递给另一个函数的函数，后者在完成后调用回调函数。

```
funcA(dataA => {
  funcB(dataB => {
    funcC(dataC => {
      ...
    })
  })
}); 
```

这种嵌套可以变得相当疯狂，如果你不相信我只是谷歌“回调地狱”。为了补救这一点，语言中引入了承诺。

### 承诺

JavaScript 中增加了承诺来消除回调地狱。

承诺有三种可能的状态:待定、履行或拒绝。当一个承诺被实现时，它解析(使用 resolve(val))为一个值，当它被拒绝时(使用 reject(val))，它返回一个未解析的原因。当解决时，承诺触发 then 子句，当拒绝时，它触发 catch 子句。

```
new Promise((resolve, reject) => {
    // resolve('Resolved');
    // reject('Rejected');
  })
  .then(value => {
    console.log(value);
  })
  .catch(err => {
    console.log(err);
  }); 
```

这听起来可能有点令人困惑，但是看了几个例子后应该就明白了。

```
const wait = time => new Promise((resolve) => setTimeout(resolve, time));

wait(1000).then(() => console.log('Resolved!')); 
```

下面是一个使用 Fetch API 的例子，它返回一个承诺并异步处理一个 HTTP 请求。如你所见，你可以把承诺串在一起形成一个链条。通常，在末尾添加一个`catch`来捕获链中发生的任何错误。

```
fetch(url)
  .then(response => {
    return response.json();
  })
  .then(myJson => {
    console.log(JSON.stringify(myJson));
  })
  .catch(err => {
    throw new Error(err);
  } 
```

### 异步/等待

最新最好的方法是使用 async/await。

异步函数使您能够编写基于承诺的代码，就像它是同步的一样。异步函数总是返回一个承诺(返回的不是承诺的值自动包装在一个带有原始返回值的解析承诺中)。

```
async function foo() {
  return "Async!";
}

foo().then(value => console.log(value)); 
```

await 操作符用于等待承诺。需要注意的是，这只能在异步函数中使用。

```
async function foo() {
    let promise = new Promise((res, rej) => {
        setTimeout(() => res("Resolved"), 2000)
    });

    // wait here for promise to resolve...
    let result = await promise; 

    console.log(result); 
};

foo(); 
```

## 变量

Javascript 变量有三种风格

*   var:功能范围
*   let:阻止范围
*   const: block 限定范围且不可变(一旦设置就不能更改)

```
function foo() {
    var a = "A";
    let b = "B";

    if(true) {
        console.log(a); // A
        console.log(b); // B

        var c = "C";
        // Scoped to the if block
        let d = "D";
    }

    console.log(c) // C
    console.log(d) // d is not defined here!

    const e = "E";
    e = "F" // Error! Cannot re-assign const
} 
```

## 类

Javascript 类与传统面向对象语言中的类相似。

```
class Vehicle {
    // Class constructor (initialized with new Vehicle(...))
    constructor(model, make, year) {
        this.model = model;
        this.make = make;
        this.year = year;
    }

    // Getter
    get makeAndModel() {
        return `${make} ${model}`;
    }

    // Setter
    set year(year) {
        this.year = year;
    }

    // Class function
    getDescription() {
        return `A ${year} ${make} ${model}`;
    }
}

class CoolVehicle extends Vehicle {
    getDesciption() {
        return `A cool ${year} ${make} ${model}`;
    }
} 
```

> 在一个类构造函数中，你可能会看到`super(...)`，它被用来引用父类。

## 导入/导出

使用`export ...`
导出模块或代码

```
export const foo = "Foo";
export function bar() { ... };
export default function defaultFunc() { ... };
const myConst = "hey";
export myConst as Hey; 
```

使用`import ... from ...`
导入模块或代码

```
// Import all exports from module
import * from 'module'; 
// Import all exports from module addressable as myModule.foo
import * as myModule from 'module';
// Import default export
import foo from 'module';
// Import named exports
import { foo, bar } from 'module'; 
```

## 时态文字

```
const firstName = "Michael";
const lastName = "Scott";
// Using temporal literals to create a string from the variables
const fullName = `${firstName} ${lastName}`; // Michael Scott 
```

## 传播算子

您可以使用 spread 运算符`...`扩展数组、对象或字符串。

```
const arr = ['a', 'b', 'c'];
const arr2 = [...arr, 'd'] // ['a', 'b', 'c', 'd']

const obj = { firstName: 'Michael', lastName: 'Scott' };
// firstName key overwrites spreaded one because it comes after it
const obj2 = { ...obj, firstName: 'Mikey' }; // Mikey Scott 
```

## 销毁作业

使用析构赋值来析构数组或对象。

```
[a, b, ...rest] = [10, 20, 30, 40, 50];
console.log(a); // 10
console.log(b); // 20
console.log(rest); // [30, 40, 50]

{a, b, ...rest} = {a: 10, b: 20, c: 30, d: 40};
console.log(a); // 10
console.log(b); // 20
console.log(rest); // {c: 30, d: 40} 
```

## Null vs Undefined

**null** 为空或不存在的值，必须赋值。

```
let foo = null;
console.log(foo); // null 
```

**undefined** 通常是指一个变量已经声明但还没有定义。

```
let foo;
console.log(foo); // undefined 
```