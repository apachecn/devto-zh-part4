# 一劳永逸:JavaScript 中的 const 不是不可变的

> 原文：<https://dev.to/valentinogagliardi/once-and-for-all-const-in-javascript-is-not-immutable-2gop>

**我知道这已经被说过几百次了**，但是我仍然看到有人宣称(甚至在一些 JavaScript 书籍中)`const`是不可变的。**它不是**。

## const 在 JavaScript 中不是不可变的

在 JavaScript 中，值可以存储在带有关键字`var`的变量中，这是声明变量最兼容的方式:

```
var greet = "Hello";
var year = 89;
var not = false; 
```

Enter fullscreen mode Exit fullscreen mode

我说兼容是因为在 ECMAScript 2015 中我们有另外两个选项:`let`和`const`。旧的浏览器可能不支持这些新的关键字，除非使用像 Babel 这样的“transpiler ”,否则你可能会遇到错误。在新的浏览器中，你可以获得`let`和`const`的好处，它们在两个方面与`var`不同:

*   `let`和`const`都创建自己的“气泡”(范围)
*   不能重新分配，也不能重新声明

我所说的“气泡”是指用`let`或`const`声明的变量不会与在封闭或外部“气泡”中声明的相同变量名重叠。比如:

```
let name = "John";

{
  let name = "Valentino";
  console.log(name); // "Valentino"
}

console.log(name); // "John" 
```

Enter fullscreen mode Exit fullscreen mode

这里的`name`看似重复，但实际上是两个不同的变量在各自的泡沫中。`const`有同样的行为:

```
const name = "John";

{
  const name = "Valentino";
  console.log(name); // "Valentino"
}

console.log(name); // "John" 
```

Enter fullscreen mode Exit fullscreen mode

带有`var`的相同代码以不同的方式运行:

```
var name = "John";

{
  var name = "Valentino";
  console.log(name); // "Valentino"
}

console.log(name); // "Valentino" 
```

Enter fullscreen mode Exit fullscreen mode

正如我所说的`const`不能被重新分配，也不能在同一个泡泡中被重新声明。如果你试图重新声明一个`const`，你会得到“语法错误:标识符已经被声明了”。如果你给同一个常量重新赋值，你会得到“类型错误:常量变量赋值”。下面的例子抛出了一个错误:

```
const name = "John";
const name = "Valentino";

// SyntaxError: Identifier 'name' has already been declared 
```

Enter fullscreen mode Exit fullscreen mode

这段代码也抛出:

```
const name = "John";
name = "Valentino";

// TypeError: Assignment to constant variable. 
```

Enter fullscreen mode Exit fullscreen mode

但是请注意，因为当我们说“常量不能被重新赋值，也不能被重新声明”时，**并不意味着`const`是不可变的**。这是一个几乎每个和我交谈过的 JavaScript 开发者都会被**绊倒的话题。事实上，任何稍微复杂一点的 JavaScript 数据结构，比如数组或对象，都不仅仅是可变的**，即使当它们被赋值给一个`const` :

```
const person = {
  name: "John",
  age: 21
};

person.name = "Valentino";

console.log(person);

// { name: 'Valentino', age: 21 }
// Oh, I wish I was 21 for real! 
```

Enter fullscreen mode Exit fullscreen mode

这怎么会是不可改变的？这里有一个数组:

```
const list = [1, 1, 3, 2, 5];

list.shift();

console.log(list); // [ 1, 3, 2, 5 ] 
```

Enter fullscreen mode Exit fullscreen mode

还是那句话，**不是不可变的**。下次有人说“常量是不可变的”时，给他/她展示几个技巧。

编码快乐！