# 在 JavaScript 中析构时的默认值

> 原文：<https://dev.to/paramharrison/default-values-while-destructuring-in-javascript-51c7>

在展示这个例子之前，我们将看看如何在基本的例子中进行析构

#### 破坏物体

```
// for simple object
const obj = {
  name: 'Param',
  city: 'Tallinn',
  age: 20,
  company: 'Learn with Param OU',
};

const { name, age, ...rest } = obj;

console.log(name); // Param
console.log(age); // 20
console.log(rest); // { city: 'Tallinn', company: 'Learn with Param OU', } 
```

#### 解构数组

```
const personArr = [
  {
    name: 'Param',
  },
  {
    name: 'Ahmed',
  },
  {
    name: 'Jesus',
  },
];

const [first, ...restOfArr] = personArr;

console.log(first); // { name: 'Param' }
console.log(restOfArr); // [{ name: 'Ahmed' }, { name: 'Jesus' }] 
```

### 析构未定义的变量

如果我们在对象中析构一个未定义的键会发生什么？例，

```
const firstObj = {
  name: 'Param',
  city: 'Tallinn',
  age: 20,
  company: 'Learn with Param OU',
};

const { firstName, city } = firstObj;

console.log(firstName); // undefined
console.log(city); // Tallinn 
```

### 析构时的默认值

现在我们可以在析构的时候传入缺省值，如果它没有被定义，它将采用缺省值。例，

```
const secondObj = {
  firstName: 'Param',
  country: 'Estonia',
};

const { lastName = 'Harrison', country } = secondObj;

console.log(lastName); // Harrison
console.log(country); // Estonia 
```

在这个例子中，即使`lastName`没有在 object 中定义，它也采用我们在析构时赋予的默认值。

即使对象中没有值，当您希望在应用程序中设置默认值时，这将非常有用。

#### 析构中设置默认值时的边缘情况

这是一种极端的情况，如果默认值没有定义，析构就可以很好地使用默认值。它不适用于其他非真值，例如`null`、`0`、【false】。

让我们看一下例子，

```
const edgeCase = {
  why: undefined,
  what: null,
  how: 0,
  where: false,
};

const {
  why = 'why',
  what = 'what',
  where = 'where',
  how = 'how',
  notFound = 'Not Found',
} = edgeCase;

console.log(why); // why
console.log(what); // null
console.log(how); // 0
console.log(where); // false
console.log(notFound); // Not Found 
```

希望你喜欢并学到了一些关于 javascript 析构的技巧😎

在这里检查整个例子，

[https://repl.it/@ParamHarrison/destructuring-with-default-values?lite=true](https://repl.it/@ParamHarrison/destructuring-with-default-values?lite=true)