# Javascript 中的高阶函数

> 原文：<https://dev.to/ganeshmani/higher-order-function-in-javascript-javascript-weekly-3c8i>

在这篇文章中，我们将看到什么是 javascript 中的高阶函数，以及如何实现它们。Javascript 中的高阶函数。

[原型继承- Javascript](https://cloudnweb.dev/2019/07/prototypal-inheritance-javascript-weekly/)

[Javascript 中的 Currying】](https://cloudnweb.dev/2019/07/currying-in-javascript-javascript-weekly/)

[理解 Javascript 中的闭包](https://cloudnweb.dev/2019/07/understanding-closures-in-javascript-javascript-weekly/)

### 什么是高阶函数？

主要地，高阶函数是将函数作为自变量或者返回函数的函数。如果一个函数满足以上任何一个条件，那么它就是一个高阶函数。

让我们用一个简单的例子来理解这个概念。

```
const func1 = (data,fnargument) => {
  const newData = fnargument(data);
  return newData
}

const fnargument = (value) => {
  //Do some Logic Here
}
```

**func1** 带两个参数，一个是数据，一个是函数。

javascript 中的回调是另一个例子

**回调**在 javascript 中也称为高阶函数，是传递给另一个函数的函数。

### Javascript 中的高阶函数

如前所述，javascript 中的回调也称为高阶函数。javascript 中高阶函数的一些例子有 **map()** 、 **reduce()** 和 **filter()** 。

让我们以 **filter()** 为例，从头开始实现它。

让我们看看 Javascript 是如何实现内置的 **filter()** 函数的。

```
checkUserCountry = ({name, country}) => country === 'US';

const user = [
  {
    name : 'Steve',
    country : 'US'
  },
  {
    name : 'james',
    country : 'UK'
  },
  {
    name : 'stuart',
    country : 'US'
  }
]

const userList = user.filter(checkUserCountry);

console.log(userList);
```

最重要的是， **filter()** 函数接受一个函数 **checkUserCountry** ，该函数根据国家进行过滤

现在，让我们从头开始看看如何实现 **filter()** 函数。

### **编写我们自己的高阶函数**

**filter()** 函数循环遍历一个数组，并将值传递给一个函数，该函数根据条件返回布尔值。

```
function filter(arr,fn) {

    let newArr = [];
    for(var i=0;i< arr.length;i++){
      let value = arr[i];

      newArr = fn(value) ? [...newArr,value] : [...newArr]
    }
    return newArr;
} 
```

**filter()** 函数有两个参数，**数组**和一个**函数**。之后，它初始化一个变量 **newArray**

```
let newArr = [];
```

之后，我们遍历数组并将值存储在变量 **value** 中。将其传递给自变量函数 **fn** 。

```
 for(var i=0;i< arr.length;i++){
      let value = arr[i];

      newArr = fn(value) ? [...newArr,value] : [...newArr]
    }
```

之后，argument 函数根据值返回 true 或 false。如果返回 true，该值将被存储在 **newArr** 中。

最后，我们从函数中返回 newArr。

#### 完全码

```
checkUserCountry = ({name, country}) => country === 'US';

const user = [
  {
    name : 'Steve',
    country : 'US'
  },
  {
    name : 'james',
    country : 'UK'
  },
  {
    name : 'stuart',
    country : 'US'
  }
]

function filter(arr,fn) {

    let newArr = [];
    for(var i=0;i< arr.length;i++){
      let value = arr[i];

      newArr = fn(value) ? [...newArr,value] : [...newArr]
    }
    return newArr;
} 

const userList = filter(user,checkUserCountry)

console.log(userList);
```

首先，高阶函数是 Javascript 中的一个重要概念。javascript 中高阶函数的主要用法之一是在 **React** (高阶组件)中

我们将在下一篇文章中看到如何在 React 中实现高阶组件。

要了解更多关于高阶组件的信息

Eric Elliot [关于高阶元件的文章](https://medium.com/javascript-scene/do-react-hooks-replace-higher-order-components-hocs-7ae4a08b7b58)

在那之前，快乐编码:-)