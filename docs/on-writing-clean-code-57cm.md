# 编写干净的代码

> 原文：<https://dev.to/ganderzz/on-writing-clean-code-57cm>

*警告:您在下面看到的可能是主观的*

我们都同意编写干净的代码很重要。这使得在团队中工作更容易，即使我们是一个单独的开发人员，干净的代码也能让我们未来的自己开心。曾经看着你的旧代码，想知道，“这是什么垃圾？”如果你说“不”,那就多写点代码——感觉就会来了。对于那些回答“是”的人，让我们来看看一些可以提高代码可读性的技术。

## 用好名字

想出一个好名字很难，但任何东西都比用‘foo’好。当创建名字时，使用一些描述性的东西:变量包含什么？该功能的目的是什么？记住，我们写代码是为了让人类阅读！

没有代码的上下文，很难说“这就是你应该命名的东西”，但是在选择名字的时候，有一些指导方针我们可以使用。

### 布尔

布尔用于将代码分成两条路径(对/错..是/否)。命名布尔值时，提出一个问题会有所帮助。例如，我们可能有一些看起来像这样的代码:

```
let content = false;

if (content) {
  ...
} 
```

这是可以的，但是它并没有给出`content`是什么的任何上下文。在命名布尔值时，给它加上前缀`is`、`are`、`should`、`has`等...只是让变量提出一个问题。

```
let hasContent = true;

if (hasContent) {
  ...
} 
```

### 列表/数组

使列表/数组成为复数——它们可以保存多个值，所以要在命名中反映出来！

```
let salesOrder = []; // :(
let salesOrderList = []; // :|
let salesOrders = []; // :D Yay! 
```

### 不要害怕走远

有时一个单词并不能明确解释一段代码的作用。比如函数应该做什么，或者变量包含什么。用一个*句子*来命名一个东西完全没问题，只是要注意我们是否开始用*段落*来命名我们的变量

```
function doStuff() { ... } // Bad!

function getSalesOrder() { ... } // Better!

function getSalesOrderByLineId() { ... } // Even Better!

function getSalesOrderByItemIdInTheDatabaseThenValidateReturnObject() { ... } // What? Bad! 
```

### 让类型检查器完成它的工作

这主要适用于具有类型检查的编程语言。使用上面的变量名`salesOrderList`的例子，不要在名称中指定变量的类型。类型检查器已经给了我们这些信息。切换语言(稍微)，让我们看一个 Typescript 中的例子。

```
let salesOrderList: SalesOrder[] = []; // We already know the type!
let salesOrders: SalesOrder[] = []; // Better!

let nameString = "Name"; // :(
let name = "Name"; // :D 
```

### 使用语言的特性

除了利用类型检查器之外。看看好好利用一门编程语言的其他分组特性吧！我所说的‘分组特性’是指像**类**和**名称空间**这样的东西。通常不需要在一个类中重复命名空间的名称。类似地，这同样适用于方法中重复的类名。

```
/* It's easy to get lost what's going on. (SalesOrder.SalesOrder.SalesOrder(...)) */
namespace SalesOrder {
  class SalesOrder {
    function SalesOrder() {
      ...
    }
  }
}

/* 
 * Following our naming structure, it's extremely easy to figure out what's going on 
 * while not repeating ourselves.
 * (Repositories.SalesOrder.GetById(...))
 */
namespace Repositories {
  class SalesOrder {
    function GetById() {
      ...
    }
  }
}

/* 
 * This is also perfectly fine, as namespaces generally get include/using/import 'd away 
 * to not be seen.
 * (SalesOrderRepository.GetById(...))
 */
namespace Repositories {
  class SalesOrderRepository {
    function GetById() {
      ...
    }
  }
} 
```

## 空白

不，这不是制表符对空格。挑一个，然后跟着它走。我所说的空白是指代码语句之间一致的换行符和空白非常有价值。就像我们将代码的逻辑部分分成类/函数/文件等，我们也应该用空白分隔代码的显式块。

```
// Anyone else going cross-eyed? Remember, write code for humans!
function add(a, b) {
  return a + b;
}
function doStuff(a, b) {
  let j = add(a, b);
  if (j > 2) {
    j = 5;
  }
  else {
    return a;
  }
  for(let i = 0; i < 20; i++) {
    j += i;
  }
  return j;
} 
```

```
// Much better! Statements are separated by new-lines to give context,
// and makes it a lot easier to read!
function add(a, b) {
  return a + b;
}

function doStuff(a, b) {
  let j = a + b;

  if (j > 2) {
    j = 5;
  }
  else {
    return a;
  }

  for(let i = 0; i < 20; i++) {
    j += i;
  }

  return j;
} 
```

总结一下，在前后至少留一个空行:

*   **if/else** 语句
*   **for/while** 循环
*   **函数/方法/类/名称空间**

前面还有一个空行:

*   **在函数结束时返回**语句

## 功能/方法

我们可以从函数式编程中汲取很多有用的思想([我在这里给大家介绍一下函数式编程！](https://www.dylanpaulus.com/2019-03-31-functional-programming/))说到写干净的代码。让我们来看看函数式编程范例中的一个想法。

### 只做一件事！

函数/方法应该只做一件事。实践这一理念使得测试函数更加容易，交换函数或其他函数变得微不足道，调试变得轻而易举！

在命名我们的函数时，我们是优秀的程序员，并给出一个好的描述性名称。但是，如果我们在名字中看到`or`或`and`，就会发现一个函数做了比它应该做的更多的工作。

```
// Common occurance of OR!
function updateOrCreate() {
  ...
}

// Better!
function update() {
  ...
}

function create() {
  ...
} 
```

在命名函数时，不要只是避免使用`or`或`and`——要对自己诚实！这将有助于保持您的代码更易于维护！

* * *

### 最后，

# 要一致！

编写干净代码最重要的一点是保持一致，即使你最终没有使用上面的任何一种风格。让你和你的团队保持相同的编码风格会让你更容易跟上当前项目的进度，以及任何未来的努力。