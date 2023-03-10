# JavaScript 变量和数据类型捆绑包

> 原文：<https://dev.to/abhinavdotdev/javascript-variables-and-data-types-bundle-52ij>

每种编程语言都需要某种东西来存储信息。处理和操作数据所需的信息。JavaScript 对这种风格并不陌生。

```
alert("Welcome to JavaScript!"); 
```

信息是数据的集合。信息可以以不同的形式提供给我们。一些例子可能是:

*   家谱:家庭成员的名字，年龄，等级等等。是一个信息
*   电话簿:名单中的人数、他们的地址和联系方式都是电话簿中收集的信息。

**JavaScript 变量**

变量可以被认为是数据的容器。假设你有六个苹果。现在，你需要一个盒子来装它们。这个盒子是 JavaScript 中的一个变量，用来存储这 6 个苹果。JavaScript 允许它的变量存储任何类型的数据。允许这种简单访问的编程语言被称为“动态类型”。这意味着一个变量可以保存一个数字甚至字符串，而不需要显式声明。

```
var box = "apple";
var name = "John Doe";
var number = 1; 
```

现在我们知道什么是变量了。问题是我们的系统将如何理解变量。因此 JavaScript 允许我们使用三种类型的变量声明。
**var 关键字**:在前面的例子中，我们已经多次使用 var。在 var 中声明的变量不会丢失它们的值。他们有全球性的视野。它们的范围在函数边界内扩展，或者它们是全局的。

```
var number = 123; 
```

**let 关键字** : let 关键字也用于声明变量，但是 let 的范围仅限于它所在的块。它的值不能在它的块之外访问。它的声明方式也和 var 一样。我们现在不讨论细节。我们将进一步详细讨论它们。

```
let number = 123; 
```

**const 关键字**:使用 const 关键字声明的值不能更改。如果试图这样做，你会得到一个错误。const 下存储的值是常量“不可改变”。

```
const number = 123; 
```

这就是我们现在需要知道的关于在 JavaScript 中声明变量的全部内容。我们将在后面的讨论中详细研究这些变量声明，看看它们是如何处理数据的。

**JavaScript 数据类型**

**数字**
一个数字全部是整数和浮点数。所有种类的数学运算，如加、减、乘、除，都可以用数字来完成。

```
let n = 123;
n = 12.35; 
```

字符串是用引号括起来的任何东西。它可以是单引号或双引号。JavaScript 并不区分它们。

```
let name = "John Doe"; 
```

**布尔值**
一个布尔值有两个值，要么是“真”，要么是“假”。它检查值或条件是否正确。我们将在后面详细讨论关于布尔的问题。

```
let isGreater= 4 > 1;
alert(isGreater); 
```

**Null**
Null 在 JavaScript 中代表“空”或者“没有值”。

```
let age = null; 
```

**undefined**
在 JavaScript 中 undefined 的意思是“值未定义”。在 JavaScript 中，某些特定函数可能会返回未定义的值。

```
let x = undefined; 
```

**对象**
对象是非原始数据类型。对象可以是用于在单个容器中存储数据类型甚至类似数据的容器。我们现在不需要详细讨论对象。

```
function createUser(name,batch,marks,maxmarks){
    this.name=name;
    this.batch=batch;
    this.marks=marks;
    this.maxmarks=maxmarks;
}

createUser.prototype={
    percent:function calcPercent(){return this.marks/this.maxmarks*100;},
    grade:function calcGrade(){return this.marks>400?"A":this.marks>300?"B":this.marks>200?"C":"D";}
}
var abhinav= new createUser("abhinav",9,450,500); 
```

**typeof 运算符**
typeof 运算符返回数据的类型，就这么简单，可以用来快速检查。它的语法是 typeof(变量名)。

```
typeof 0;//number
typeof "foo";//string 
```

* *这就是我们现在需要了解的 JavaScript 变量和数据类型。当然，这还不是全部，但是我们将在后面的章节中看到它们的作用，并对它们的用法有更好的理解。

在那之前，祝你学习愉快！**