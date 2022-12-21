# JavaScript 中的循环及示例

> 原文：<https://dev.to/mugas/loops-in-javascript-with-examples-4bh0>

自从我们开始永无止境的学习 JavaScript(或任何编程语言)之旅以来，循环就一直伴随着我们，所以让我们来关注它们。对我来说，最好的学习方法是用我所学的知识创造一些东西，所以我试着为所有的循环提供例子，你可以看到它们做了什么，以及我们如何使用它们

## 我们将覆盖哪些循环

* [while loop](#while%20loop)
* [do...而循环](#do...while%20loop)为
* [为](#for)为
* [为..3 .在](#for...in)中
* [为...](#for...of)的

所以首先让我们定义什么是**循环**以及为什么我们需要在 JavaScript 中使用它们？
你知道当你非常喜欢一首音乐的时候，你会一遍又一遍地听吗？这是一个循环。对于编程来说，循环是一种迭代数组或对象的方式。使用循环，我们可以确保我们的代码是干燥的。**循环**将循环直到满足条件，或者如果满足条件则循环，直到条件为假。

无限循环是一个没有结束的循环，很可能会使你的应用程序/网站崩溃

### **而循环**

只要评估的条件为真， **while 循环**就会运行。把它想成一个条件，一个 ***if*** 语句但是代替运行一次，只要条件为真就运行。如果你不写条件或者写一个永远不会变假的条件，也就是说，永远为真，循环永远不会结束。

**语法**:

```
while(condition)
{
  //code to be executed as long the condition is true
} 
```

让我们打印 0 到 5 之间的所有数字:

```
 let i = 0;
  while (i < 6) {
    console.log(i);
    i++;
  } 
```

这里发生了什么？首先，在**循环**之外，将变量设置为 0。然后你写 while 条件为`i < 6`，所以只要 *i* 小于 6，上面的代码就会运行。
代码是什么？括号内的所有内容，所以在这种情况下，打印变量(数字)并给变量加 1。

所以从 0 开始。零小于 6？没错，所以打印出 *i* 即 0 加 1。*后的我*是多少？是的，它是 1，仍然低于 6，所以再做一次，直到 *i* 是 6。因为 6 不小于 6，所以**循环**结束，打印出来的是:

```
let i = 0;
  while (i < 6  ) {
    console.log(i);
    i++;
  }

  /*
  0
  1
  2
  3
  4
  5 
  */ 
```

### 现实点吧

对我来说，最好的学习方法是看到情况/问题的可行性，在这种情况下是循环。我会尽我所能做到最实际，但是如果我说得不够清楚或者我如何改进，请随时告诉我。

这只是一个小例子，我想说的是没有什么是永远不会被需要的，至少像这样:)

现在是除夕，你想创建一个小的 10 秒倒计时，直到 0 或直到新年，说还有多少秒到除夕，当达到 0 时，它会说“新年快乐”。

[https://codesandbox.io/embed/while-loop-oevd6](https://codesandbox.io/embed/while-loop-oevd6)

这里发生了什么？

我不会详细介绍 DOM 操作，但是基本上我们在我们的 **html** 中设置了一个`id`，然后我们在我们的 js
`const message = document.querySelector("#container")`中设置了一个`id`，在那里我们将显示我们的消息`message.innerHTML=`${countdown}`。我还使用了[模板文字](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals)。

现在是 **while 循环**。我们将变量*秒*设置为 10，这是我们想要开始它的地方，然后我们将变量*倒计时*设置为一个空字符串，我们将在 JS 中打印倒计时。

然后我们的 **while 语句**说只要 seconds 变量大于 0，我们就写消息并将变量 seconds 设置为*减*一秒。
在我们刚刚设定它到达零时。所以是外循环
`countdown =` `${countdown} ${seconds} Happy new Year`。

因此 *const* 消息将在倒计时期间显示倒计时变量，并相应地显示消息。

### **做什么...同时循环**

**做…而**的工作有点不同。最大的区别是 **do…而**循环至少运行一次。基本上就是说，当这个条件成立时，至少运行一次。

**语法**:

```
do

  //statement or code to execute

while(condition) 
```

现在让我们来看看它只运行一次的情况:

```
let i = 0;

do {
  i++;
  console.log(`I run at least ${i} times`);
} while (i < 0);

console.log(`I run a total of ${i} times`);
// expected result: "I run at least 1 time"
// "I run a total of 1 times" 
```

我们将变量设置为 **0** ，并设置循环在每次迭代时加 1，并且只要 I 小于 0 就这样做(*将条件设置为*)。我将一个 console.log 放在语句内部，另一个放在语句外部，以查看它输出的内容。

所以首先 **i** 是 0，我们加 1，打印“我至少跑了 1 次”。然后我们检查条件:I 小于 0 吗？好了， **i** 现在是 1，大于 0，所以循环停止，它会打印“我总共运行了 1 次。
如果你想看到差异，例如将条件改为 5

```
let i = 0;

do {
  i++;
  console.log(`I run at least ${i} times`);
} while (i < 5);

console.log(`I run a total of ${i} times`);
/* expected result:
"I run at least 1 time"
"I run at least 2 time"
"I run at least 3 time"
"I run at least 4 time"
"I run at least 5 time"
"I run a total of  5 times"*/ 
```

这里你可以看到当它循环不止一次时它是如何工作的。I 从 0 开始，然后我们加上 1，它变成了 1。上面印着“我至少跑一次”。然后，因为 I 仍然小于 5，所以再加 1，这样做，直到 I 为 5，然后打印以上所有内容。

### **为循环**

JavaScript 中最常用的循环之一是循环的**。当我开始用 JS 编码的时候，这是我到今天为止用得最多的一个。在开始的时候，我不太明白它是如何工作的，所以当我在 for 循环中开始
的时候，我试着用一种我能理解的方式来解释，代码会一直重复，直到条件评估为 false。**for 循环的用途之一是循环遍历一个数组**。**

**语法**:

```
for([initialization];[condition]; [final-expression]) 
```

所以在使用时，看起来会像这样:

```
for(let i =0; i <5 ; i++){
    //do something
} 
```

为了解释，我们先来看一个简单的例子。你想循环从 0 到 4 的所有数字并打印它们，上面的循环就是要用的那个。

所以初始化( **i=0** )是我们定义变量的地方，因为我们想从 0 开始，我们把变量定义为 0。

条件( **i < 5** )是一个表达式，在每一个循环的结尾都将被求值，当它为假时，循环停止。所以在这种情况下，在每个循环中，检查 I 是否小于 5**。**

最终表达式( **i++** )通常用作增量。你需要考虑的是，最终表达式出现在条件被求值之前

**//做某事**部分是只要条件(i < 5)为真，它就会运行的代码。
在 for 循环中，你可以使用 break 或 continue 语句。

那么我们来看一个更真实的例子。你有一个网站，你想添加你看到的电影，并在屏幕上显示它们。首先，在我们的 html 中，让我们创建一个 div，在这里我们将展示我们的电影。

```
<h1>Movies I see</h1>
      <div id="movies"></div> 
```

在我们的 js 中，我们创建了包含电影的数组，我们可以添加或删除更多的电影。

```
let movies = [
  "Constant Gardener",
  "Batman",
  "Pulp Fiction",
  "Heat",
  "Godfather"
]; 
```

现在让我们用一个 **getElementById** 从 html 中获取 div:

```
let myMovies = document.getElementById("movies"); 
```

然后，我们创建一个空字符串，在那里我们将渲染所有的电影。

```
let myList = ""; 
```

现在，我们想循环播放现有的电影，我们可以用 for 循环来创建它。

```
for (let i = 0; i < 5; i++) {
    console.log(movies[i]);
  } 
```

那么循环中会发生什么呢？首先我们创建一个变量并设置为 **0** 。为什么是 0？从我们的第一个元素开始。如果我们把它改成 1，你会看到它不会打印电影《常数加德纳》。我们设定的条件是，在 **i** 小于 5 时进行打印。为什么是 5？因为我们有很多电影。然后我们添加 **i++** 给每个循环总是加一。然后，我们只需要在每个循环中添加我们想要的东西，在这种情况下，我们只想 console.log 它——我们编写 **movies[i]** 来分别编写每个电影。如果你只写

```
console.log(movies); 
```

它将打印电影数组 5 次，而不是 5 部电影。

我们能做些什么来改善它呢？如果你想再加一部电影呢？如果你有另一个条件，你必须把条件改为 **i < 6** 和 7，依此类推。这不是真正的生产。所以让我们把它变成动态的。

我们希望它一直循环，直到我们用来循环的变量( **i** )小于电影的数量，对吗？在编程中，数组中元素的数量(本例中是电影的数量)是数组的**长度**，所以我们可以这样写代码:

```
for (let i = 0; i < movies.length; i++) {
    console.log(movies[i]);

  } 
```

就像这样，如果我们添加另一个电影，我们不需要担心，因为它会一直循环。现在让我们也在屏幕中渲染。我们可以通过在每次循环时创建一个项目符号来实现。

```
for (let i = 0; i < movies.length; i++) {
    console.log(movies[i]);

    myList = myList + `<li>${movies[i]}</li>`;
  } 
```

我们在这里做了什么？所以我的列表是一个空字符串，对吗？所以在每一次循环中，我们希望在我们循环空字符串的时候，它是数组的一个元素。

为了让代码更好，让我们把所有东西都放在一个函数周围

```
function myMovies() {
  let myMovies = document.getElementById("movies");
  let myList = "";
  for (let i = 0; i < movies.length; i++) {
    console.log(movies[i]);

    myList = myList + `<li>${movies[i]}</li>`;
  }

  myMovies.innerHTML = `${myList}`;
}

myMovies(); 
```

现在我们用我们创建的函数创建 HTML 元素，并呈现在 **myList** 上的数据。

[https://codesandbox.io/embed/forloop-kcmf8](https://codesandbox.io/embed/forloop-kcmf8)

### **因在**

因此相应地以 [MDN](https://developer.mozilla.org/en-US/) 、**T3 为...在** 中，迭代对象的可枚举属性，例如 **object.keys** 。例如，构造函数或原型属性不被认为是可枚举的，所以当在中为…运行**时，你看不到它们**

所以即使在 Js 中，所有东西都是一个[对象](https://gomakethings.com/everything-is-an-object-in-javascript/)，你也不应该在[数组](https://stackoverflow.com/questions/500504/why-is-using-for-in-with-array-iteration-a-bad-idea)中用**来表示**。这样做的主要原因是中的**for…以任意顺序迭代，当迭代一个数组时，索引很重要。所以我们关注为什么以及如何在一个对象中使用它们**

**语法**:

```
for (let key in object) {
 //code in the loop
} 
```

所以这里的**键**名就是给对象赋值的名字变量。是 for 循环中的 *i* 。就像 for 循环中的 *i* 一样，你可以给它取任何名字。**对象**是实际的对象，所以你将把你要循环的对象名。
让我们看看它是如何工作的，在这个例子中它做了什么。你有一个人对象。

```
 let person = {
  name : "Steve",
  age : 35,
  city:"London"
} 
```

现在使用我们的**进行...在**中，让我们循环，看看我们得到了什么:

```
for (let key in person) {
  console.log(key);
}
//name
//age
//city 
```

我们得到了对象人的属性，对象的密钥。务必获得您可以做到的价值:

```
for (let key in person) {
  console.log(key);
  console.log(person[key]);
}
//name
//Steve
//age
//35
//city
//London 
```

让我们做得更清楚些

```
for (let key in person) {
  console.log(`${key} - ${person[key]}`);
}
//name - Steve
//age - 35
//city - London 
```

这很好，也很有用，但是当我们有一个对象构造函数时会发生什么呢？

注意:如果你第一次看到循环，对象构造器可能看起来更高级，我将在以后的文章中讨论它。对于这个例子，假设您想要创建许多 persons 对象。所以你得一个一个加。但是如果你能创建一个对象构造函数，它具有所有人都有的属性，那就很容易了，对吗？然后我们有了对象构造函数
，让我们创建那个对象构造函数。

```
let Person = function(name, yearofBirth, job) {
  this.name = name;
  this.yearofBirth = yearofBirth;
  this.job = job;
}; 
```

然后我们给这个对象添加一个函数:

```
Person.prototype.calculateAge = function() {
  console.log(2019 - this.yearofBirth);
}; 
```

现在让我们创建一些对象:

```
let Ricardo = new Person("Ricardo", 1992, "teacher");
let Marika = new Person("Marika", 1987, "designer");
ricardo.calculateAge();
marika.calculateAge(); 
```

现在让我们遍历 Marika 对象:

```
for (var key in marika) {
  console.log(marika[key]);
}

/*
Marika
1987
designer
ƒ () {
  console.log(2019 - this.yearofBirth);
}
*/ 
```

除了对象 **Marika** 的属性之外，它还对函数进行循环，这是因为中的……的**对原型链的所有属性进行迭代。因此，我们可以用 **hasOwnProperty** 方法遍历包含 key 对象的属性:**

```
for (var key in marika) {
  if (Person.hasOwnProperty(key)) {
    console.log(marika[key]);
  }
}
/*
Marika
1987
designer
*/ 
```

因此，您可以在中使用**for……来遍历属性名，并从具有某些属性(例如 key 属性)的对象中进行检查**

### **为...**

我们要讲的最后一个循环是的**for……。它适用于可迭代的对象，比如数组和字符串。它和 ES6 一起作为 **forEach**
的替代品出现。
语法类似于**中的**for……只是改变了 in/on。并且您应该仅在计划对象中使用 **for…in** ，而 **for…of** 不适用于对象。**

**语法**:

```
for (let key of object) {
 //code in the loop
} 
```

```
let ingredients = ["dough", "tomato", "cheese"];
for (let key of ingredients) {
  console.log(key);
}

//dough
//tomato
//cheese 
```

你可以马上看到，它可以做与 for 循环相同的事情，但是代码更少，更简洁，而且对字符串也有效:

```
const name = "Ricardo";

for (const key of name) {
  console.log(key);
}

/*
R
I
C
A
R
D
O
*/ 
```

也适用于**贴图**、**物体**和**布景**，但我们不会在这篇文章中关注它们。它在普通对象上不起作用，因为对象不是“可迭代”的。

但是一个很好的使用 **for…of** 的方法是在一个节点列表上。例如，如果你有一些标题在同一个页面上，你想点击改变它们的颜色**。输入**的

 **所以 html 有一堆相同类别的标题。在我们的 JS 文件中，我们用:

```
const elements = document.querySelectorAll(".names"); 
```

然后我们只需为的……加上**:**

```
for (const element of elements) {
  element.addEventListener("click", () => {
    element.classList.add("active");
  });
} 
```

**活动的**类是我们想要在点击时添加的类，它将使文本改变颜色和位置。
就这样。

[https://codesandbox.io/embed/forof-6feld](https://codesandbox.io/embed/forof-6feld)

仍然有很多关于循环的讨论，但是希望你可以开始在你的项目中使用它们，并且知道你想要使用哪一个。让我们开始编码吧。

很高兴听到你对这篇文章的反馈以及如何改进。你可以在 Instagram 上关注我，我每周都会在那里发布片段、我正在做的项目以及其他与代码相关的东西。你在我的[个人资料](https://dev.to/mugas)上找到的所有其他链接。**