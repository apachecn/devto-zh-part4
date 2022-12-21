# ES6:解构

> 原文：<https://dev.to/epicosity/es6-destructuring-30e9>

析构是一种 JavaScript 语法，允许我们将数组和可重复项中的数据赋值给变量，就像对象一样。让我们首先看看在对象上使用析构语法。

### 破坏物体

在 ES5 中，你可以通过将你的对象属性值单独分配给变量来分配属性。

ES5

```
var band = {
  name: 'Silent Planet',
  genre: 'Metalcore',
  albums: ['The Night God Slept', 'Everything Was Sound', 'When the End Began']
}

var name = band.name;
var genre = band.genre;
var albums = band.albums; 
```

Enter fullscreen mode Exit fullscreen mode

有了 ES6 析构，你可以减少那些赋值，只要你赋值的变量名和属性名匹配，如下所示。

```
let band = {
  name: 'Silent Planet',
  genre: 'Metalcore',
  albums: ['The Night God Slept', 'Everything Was Sound', 'When the End Began']
}

const { name } = band;
const { genre } = band;
const { albums } = band; 
```

Enter fullscreen mode Exit fullscreen mode

您可以通过将所有变量赋值组合成一个来缩短语法:

```
const { name, genre, albums} = band; 
```

Enter fullscreen mode Exit fullscreen mode

只有当变量名与试图析构的属性名匹配时，上述语法才有效。如果变量名不匹配，你仍然可以用下面的语法执行析构赋值:

```
const {name:var1, genre:var2, albums:var3} = band;

console.log(var1); // Silent Planet 
```

Enter fullscreen mode Exit fullscreen mode

注意左边的属性名和右边的变量名。你可以把这想象成你在取属性值->变量。从左到右赋值。

### 解构数组

在 ES5 中，为了从数组中提取数据，你需要通过获取数组中某个位置的值来访问数组:

```
var exampleArray = [1, 2, 3];

var val1 = exampleArray[0];
var val2 = exampleArray[1];
var val3 = exampleArray[2]; 
```

Enter fullscreen mode Exit fullscreen mode

有了析构赋值语法，你现在只需写一行就可以得到你需要的上述数组的所有值；

```
let [val1, val2, val3] = exampleArray;

console.log(va1, val2, val3); // 1 2 3 
```

Enter fullscreen mode Exit fullscreen mode

析构一个数组看起来很像析构一个对象，但是你只是用括号代替了花括号。

如果需要，您也可以在使用析构语法时跳过值:

```
const someArray = [4, 8, 15];

const [first,,last] = someArray;

console.log(first, last);// 4 15 
```

Enter fullscreen mode Exit fullscreen mode

如果你需要的话，你可以使用 rest 语法，我们在我之前的博文[中已经学过了，将数组中剩余的所有条目提取到一个新的数组中。](https://dev.to/epicosity/es6-default-rest-and-spread-36ce) 

```
const someOtherArray = [4, 8, 15, 16, 23, 42];

const [first, second, ...third] = someOtherArray;

console.log(first); // 4
console.log(second); // 8
console.log(third); // [15, 16, 23, 42]; 
```

Enter fullscreen mode Exit fullscreen mode

### 函数参数

析构的最后一个常见用例是将对象作为函数参数传入。下面我们有一个函数，它接受一个具有车辆属性的对象。

```
const someFunction = car => {
  const { make, model, yearBuilt } = car;
  return make;
} 

let car = {make: 'Ford', mode: 'Escort', yearBuilt: 1993};

someFunction(car); //Ford 
```

Enter fullscreen mode Exit fullscreen mode

您可以在传递给函数的对象上使用析构。函数体内的语法和我们在这篇文章中使用的模式是一样的。然而，下面有一个稍微不同的、更短的方法来做到这一点:

```
const someFunction = ({ make, model, yearBuilt }) = {
  return make;
} 
```

Enter fullscreen mode Exit fullscreen mode

你可以将对象属性析构成函数参数中的单个变量。

感谢您的阅读，我希望这篇文章能帮助您在自己的代码中使用析构。

* * *