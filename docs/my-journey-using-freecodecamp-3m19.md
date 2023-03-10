# 我使用 freecodecamp 的旅程

> 原文：<https://dev.to/vicradon/my-journey-using-freecodecamp-3m19>

因此，最近，我认为一个有组织的基于文本的教程可以帮助我更快地学习 web 开发。Freecodecamp 是解决方案。但是后来，我跳过了大部分的 HTML 和 CSS。一头扎进 JavaScript。我必须承认，它教会了我很多。我仍然在 JavaScript 轨道上。所以，这是我到目前为止学到的。

## ES6

ES6 对 JavaScript 来说是一个很大的改进。我来自 python 背景，注意到 python 中的一些技巧是使用 ES6 实现的。让我们来看看这个主题的几个话题

## 1。解构

这需要很多东西，例如:

### A .数组析构

#### 交换数值

```
let a = 2; let b = 5;
[a, b] = [b, a]
console.log(a, b) //outputs 5 2 
```

Enter fullscreen mode Exit fullscreen mode

#### 行内变量创建

```
const [a, b, c] = [1, 2, 3];
console.log(a, b, c) // outputs 1 2 3
// This gives you power to create variables faster 
```

Enter fullscreen mode Exit fullscreen mode

### B .对象析构

现在对于对象，我喜欢把它看做*浅替换*。
假设我们想要创建一个新的变量，该变量将具有来自对象的值。也许是一个带值的变量，“尼桑”

```
const cars = {a:'Nissan', b:'Toyota', c:'Benz'}
const {a:nissan_var} = cars;
console.log(nissan_var) //outputs 'Nissan' 
```

Enter fullscreen mode Exit fullscreen mode

请注意，这是从对象值创建变量的一种方式。比
干净

```
const cars = {a:'Nissan', b:'Toyota', c:'Benz'};
let nissan_var = cars.a;
console.log(nissan_var); //outputs 'Nissan' 
```

Enter fullscreen mode Exit fullscreen mode

因为如果 cars 对象中有很多条目，那么这些都是脏代码。

### 还有更多

可以写一个函数，接受一个对象，检查一个特定的键，然后析构这个键。

```
const books = { 
    fiction: ["Harry Potter", "Nueromancer", "Angels and Demons"],
    non_fiction: ["Good Leaders ask Great Questions", "Battlefield of the mind"],
};
const getDanBrown = ({ fiction }) => (fiction.find(book => book === "Angels and Demons" || book === "Inferno"));
getDanBrown(books); //returns "Angels and Demons" 
```

Enter fullscreen mode Exit fullscreen mode

我喜欢这样想。
a .析构函数中固定的对象
b .获取函数中所放内容对应的 key。在这种情况下*虚构*
c .根据函数
处理其值如果键不存在，函数返回 undefined。

假设你想从一个对象数组中析构对象。首先，您需要从数组中提取对象。让我们来看看实际情况

```
const a = [
    {m:[3, 5, 6], n:[9, 8], o:[7, 2]}, 
    {p:[3, 5]}
];
const [b, c] = a;
const {m, n} = b;
console.log(b); //outputs {m:[3, 5, 6], n:[9, 8], o:[7, 2]}
console.log(c); //outputs {p:[3, 5]}
//now working on the m object in b
console.log(m); // outputs [3, 5, 6] 
```

Enter fullscreen mode Exit fullscreen mode

所以这里的逻辑很简单。如果你想析构一个对象，你可以使用对象字面量。如果你想析构一个数组，你可以使用数组文字

```
a in the above snippet is an array
To completely destrucuture it, we need two variables in an array literal [] - [b, c]
Now that it has been destrucured, we can further destrucuture the objects, b and c;
b is an object, so destrucutring it requires an object literal {} - {m, n}
This was done to make the explanation colorful 
```

Enter fullscreen mode Exit fullscreen mode

## 2。休息和传播

### 休息符

rest 和 spread 运算符是兄弟运算符。rest 操作员打包展开的内容。更像是:
“我明天需要去欧洲旅行。我需要*休息操作员*来打包我的行李"
-然后你终于到了欧洲-
"现在，*扩展操作员*会很方便地打开这些行李"
好吧，我知道他们是干笑话。让我们看看代码

```
let arr = [1, 2, 3, 4, 5];
const [a, b, ...c] = arr;
console.log(a, b, c) //outputs 1, 2, [3, 4, 5] 
```

Enter fullscreen mode Exit fullscreen mode

rest 运算符将剩余的值打包到一个数组中。这是我们之前所做的解构之后的结果。现在，如果你正在使用函数，你可能需要将输入打包成一个数组。

```
const printThem = (...inputs) => {
    let a = ``;
    for (let i of inputs) {
       a += `${i} is your ${inputs.indexOf(i) + 1} input \n`
    }
    return a;
}

console.log(printThem([2, 3], 4, 5, [3]))
/*ouputs
"2,3 is your 1 input 
4 is your 2 input 
5 is your 3 input 
3 is your 4 input 
"
*/ 
```

Enter fullscreen mode Exit fullscreen mode

### 传播算子

spread 运算符根据上下文打开数组或对象。就这样。就像打开一瓶汽水。MDN 的这个例子很好地说明了这一点。

```
let parts = ['shoulders', 'knees']; 
let lyrics = ['head', ...parts, 'and', 'toes'];
console.log(lyrics)
//outputs ["head", "shoulders", "knees", "and", "toes"] 
```

Enter fullscreen mode Exit fullscreen mode

现在，复制一个数组只是一个用方括号
括起来的 rest 操作符

```
let arr1 = [1, 2, 3];
let arr2 = [...arr1];
arr2.push(4); 
console.log(arr2) // outputs [1, 2, 3, 4]

//concatenation is also possible
let arr1 = [0, 1, 2];
let arr2 = [3, 4, 5];
arr1 = [...arr1, ...arr2]; 
```

Enter fullscreen mode Exit fullscreen mode

对象也是如此。您可以使用扩展操作符
复制对象

```
let obj1 = {a:'letter', one:'number'}
let obj2 = {...obj1}
console.log(obj1) // outputs {a:'letter', one:'number'} 
```

Enter fullscreen mode Exit fullscreen mode

所以，你有它。我还有更多的事情要调查。但在那之前，继续编码。