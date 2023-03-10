# var 与 let & const

> 原文：<https://dev.to/sarahob/var-vs-let-const-12p>

ES6 已经存在了一段时间，并为 JavaScript 带来了很多很酷的变化。其中一个变化是我们如何声明变量。我们现在有三个选项:var、let 和 const。这篇文章试图用一种简单且有帮助的方式来解释它们。我们开始吧。

## 有一个“t0”

在 ES6 之前，我们使用 var 关键字。用 var 声明的变量可以立即初始化，但不需要。让我们看一个例子:

`var superhero = 'Batman'; // we initialized the variable immediately
var villain;
if(superhero === 'Batman'){
villain = 'The Joker'; // we initialized the variable later
}`

使用 var 时，变量要么在全局范围内声明，要么在函数范围内声明。例如:

`var name = 'Bob';
function getName(){
var name = 'Bill';
return name;
}
console.log(getName()); //logs Bill
console.log(name); //logs Bob`

在上面的例子中,“Bob”是在全局范围内声明的，但是即使我们使用相同的变量名,“Bill”是在函数范围
内声明的，因此日志记录 name 将导致“Bob ”,而日志记录 getName()将导致“Bill”。

## 让

ES6 给了我们 let 关键字。let 的工作方式类似于 var，变量可以立即初始化，也可以不初始化。用 let 我们得到块级声明范围。让我们来看一个例子:

`function varScoped(){
var num = 1;
if(num === 1){
var num = 2;
console.log('I am within function scope',num); //logs 2
}
console.log('I am within function scope too',num); //logs 2
}
function letScoped(){
let num = 1;
if(num === 1){
let num = 2;
console.log('I am within block scope',num); //logs 2
}
console.log('I am within function scope',num); //logs 1
}`

在上面的第一个函数中，我们用 var 声明了我们的变量，所以如前所述，变量将在函数范围内。尽管看起来我们在 if 块中重新声明了 num，但是我们覆盖了之前的声明，所以 num 在 if 块内外都记录为 2。

在我们用 let 声明的第二个函数中，因为 let 给了我们块级别的作用域，所以 if 块中的 num 变量与其外部的 num 变量在不同的作用域中，它们不会互相干扰，所以 num 在 if 块中记录为 2，在 if 块外部保留为 1 的值。

## 常数

最后但并非最不重要的是，我们有常数。与 var 或 let 不同，const 需要在声明时赋值。

所以我们不能这样做:

`const num;
num = 5;`

我们需要这样做:

`const num = 5;`
用 const 声明变量意味着该值不会改变，并且不能在该块范围内重新赋值。让我们看一个例子:

`function constScopedA(){
const num = 5;
if(num === 5){
num += 1; // this will throw an error: Uncaught TypeError: Assignment to constant variable.
}
}`

在上面的例子中，当我们试图重新分配变量 num 时，抛出了一个错误。不能重新分配变量标识符。

然而，因为 const 和 let 一样也是块范围的，所以我们可以这样做:

`function constScopedB(){
const num = 5;
if(num === 5){
const num = 6;
console.log(num); //log 6
}
console.log(num); //log 5
}`

if 块中的 num 变量和函数中的 num 变量在不同的作用域上，所以我们在这里没有得到错误。我们在两个不同的作用域上有两个不同的 num 常量。

关于 const 的一个重要注意事项是，您可以更改 const 值，但不能更改引用。例如，如果你声明一个对象为常量，你可以改变对象的内容。比如说:

`function constObject(){
const person = {name: 'Bob'};
person.name = 'Bill';
console.log(person.name); //logs Bill
}`

在上面的例子中，我们可以更新 person 的 name 属性，即使 person 是一个常量，常量变量也不是不可变的。但是，我们无法创建对人员的新引用。

`function constObject(){
const person = {name: 'Bob'};
const person = {name: 'Bill'};
console.log(person.name); //throws error Identifier 'person' has already been declared
}`

上面的函数将抛出一个语法错误，因为我们已经声明了一个名为 person 的常量。

以上就是用 JavaScript 和 ES6 进行变量声明的基本总结。希望你觉得有帮助:)

本帖由我的 medium 账号迁移而来:[https://medium . com/@ sarbot/declaring-variables-with-JavaScript-es6-ab 71 c0a 60768](https://medium.com/@sarbot/declaring-variables-with-javascript-es6-ab71c0a60768)