# 简单的结构

> 原文：<https://dev.to/quoll/a-simple-structure-2cmh>

这是我漫无边际地讨论我想要构建的东西的第二部分。你可以在这里找到第一部[。](http://bit.ly/2z1Brum)

# 简单结构

我将从一些基本原则开始。很有可能你已经熟悉了我将要说的一切，这很好。但也许这是新的，或者你想复习一下。不管怎样，在我进入下一步之前，我想确保我们在同一页上。

今天，大多数数据都是以记录的形式存在的。这只是一个将数据分组在一起的花哨术语。例如，我可以将姓名和年龄分组。

```
 (Mary, 32)
    (Paula, 21)
    (Andrew, 35) 
```

Enter fullscreen mode Exit fullscreen mode

(哦，你不会认为我会把我的真实年龄写在那里吧？😉)

在一个记录中，既有简单的数据类型，如字符串和数字，也有其他记录类型。任何使用过任何编程语言的人都应该对此很熟悉。每个记录的各个部分通常会有标签来引用每个特定的部分，否则开发人员就要记住哪个部分在哪个位置(比如在 Python 等语言的元组中)。所以这里的第一部分叫做*姓名*，第二部分叫做*年龄*。

在现代语言中，我们可以选择专门为数据设计一个记录，或者使用使用零件标签作为关键字的 *ad hoc* 映射结构。例如，在 Javascript 中，我们可以有:

```
 a_person = {name: "Mary", age: 32} 
```

Enter fullscreen mode Exit fullscreen mode

或

```
class Person {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }
}
a_person = new Person('Mary', 32); 
```

Enter fullscreen mode Exit fullscreen mode

后者看起来更复杂，但是当我们希望保证结构包含所需的信息，或者验证所提供的数据时，它特别有用。

一旦我们有了这个简单的记录类型，我们如何去存储多个呢？大多数语言都提供了几种方法。在 Javascript 中，您可能会使用数组。但是现在我们有了一些基本的记录元素，我要看看链表。

# 链表

链表是 CS 课程中最先教授的数据结构之一。嗯，他们是第一批被教 ***我*** 的人之一(坦白:那天我逃课了)。由于它们的局限性，它们经常被忽视，但也有它们非常有价值的时候。它们也说明了我想探索的一些事情，所以我将在这里和它们一起工作。

假设我想存储一系列数字:`1, 1, 2, 3, 5, 8, 13, 21, 34`

链表的基本思想是创建一个简单元素的菊花链，每个元素保存一个值，然后引用下一个元素。
[![Daisy chain with numbers over each flower](img/48988f3ed174fde7b6c561d3c4faa4bc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6HrqrpYZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/quoll/raw-data/master/daisy_chain.jpg) 
每个元素都需要存储值，并引用下一个元素。诀窍在于，当最后一个指向下一个时，它什么都不指。

让我们尝试一下，再次使用 Javascript】

```
class Element {
  constructor(value, next = null) {
    this.value = value;
    this.next = next;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

值得注意的是，当没有提供`next`参数时，它被设置为`null`。这仅仅意味着创建了一个元素列表，之后没有其他元素。

构建链表的一个技巧是从后面构建。这样，当您想要链中的“下一个”元素时，您已经完成了。您可以从前面开始，但是当您运行到末尾时，您需要用完额外的内存(通常在调用堆栈上)。计算机仍然从后面构建这个链，即使你的代码看起来不是这样。

但是在这种情况下，我将构建一个链，从 34 开始，然后回到前面:

```
let list = new Element(34);
list = new Element(21, list);
list = new Element(13, list);
list = new Element(8, list);
list = new Element(5, list);
list = new Element(3, list);
list = new Element(2, list);
list = new Element(1, list);
list = new Element(1, list); 
```

Enter fullscreen mode Exit fullscreen mode

这可以(也应该是！)以更友好的方式编写，但这不会让我们更接近我们要去的地方，所以我将把所有未加工的部分留在这里供您查看。

首先，你可以看到链中的最后一个元素后面没有任何东西，所以这是默认的，也就是`null`。你还会看到我一直使用一个`list`变量来保存列表的开始。所以`list`变量一直移动到列表的最近的开始处。每次一个新元素被添加到列表的开始，那么现有的`list`值被用于列表的剩余部分，然后`list`变量被更新以引用新的开始。

我一直提到“名单”和“名单的其余部分”。习惯上，一个链表的开始被称为*头*，剩余的链表被称为*尾*。注意，*头*和*尾*都是有效列表，并且*尾*列表总是比从相关联的*头*开始的列表短 1。

最终的结构是什么样的？是否如我们所料？

```
 Element {
    value: 1,
    next: Element { value: 1, next: Element { value: 2, next: [Object] } } } 
```

Enter fullscreen mode Exit fullscreen mode

似乎是这样，但是嵌套的数据很快就被切断了，所以让我们构建一个函数来创建一个可读性更好的字符串。

链表非常适合用*递归*函数来处理。我记得第一次向我展示递归的时候。我很容易理解他们所说的，但是每次我试图写一些递归的东西时，我总是很纠结。我只是不明白。但是现在我认为递归函数是理所当然的。所以*有可能*学会它们！真的。

同时，如果我掩饰了这样的概念，我道歉。求我澄清。

对于链表，递归函数通常属于标准模式:

*   对列表的第一个元素做一些事情。
*   如果有尾巴，那么在尾巴上再次调用函数。

在这种情况下，操作足够短:

*   查看列表的开头，返回列表中的内容。
*   如果没有更多的列表，那就这样吧。
*   但是如果有**是**以上的列表，那么加一个逗号然后再加上...同样的事情再次发生在名单的其余部分:

```
 function listString(element) {
      if (element.next === null) return element.value;
      else return element.value + ', ' + listString(element.next);
    }

    listString(list)
    '1, 1, 2, 3, 5, 8, 13, 21, 34' 
```

Enter fullscreen mode Exit fullscreen mode

这正是我们所期望的，所以我们知道这个结构是有效的。

用链表可以做很多事情。我们可以从数组中填充它们，或者把它们拉回到数组中。我们可以在中间插入额外的数字，我们可以删除元素，我们可以反转它们，我们可以依次处理每个元素…可能性还在继续。在我们想要更新结构的上下文中，这些操作中的许多都非常重要，但是这次讨论将集中在很少更新的结构上，所以我们可以把它留到下次讨论。

相反，我们正在研究如何在存储中表现这些结构。为了做到这一点，下一篇文章将考虑这个链表结构在一种更老的语言中可能是什么样子，比如 c。

继续第三部分。