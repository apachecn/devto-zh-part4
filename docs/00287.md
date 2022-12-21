# 以下是你必须始终遵循的 JavaScript 大最佳实践

> 原文：<https://dev.to/umerkk164/here-are-top-5-best-practices-for-javascript-you-must-always-follow-fdn>

JavaScript 是最灵活、最宽松的编程语言之一，相比之下，Java 或 C++之类的语言有更严格的规则。这意味着我们经常会遇到意想不到的行为，即使我们编写的代码与之前编写和测试过的代码相同。作为程序员，我们确实*不*喜欢任何意想不到的*(当然，Math.random()除外)。我们都爱 Math.random()！所以我们努力减少这些“错误”的代码编写方式，这些方式在任何时候都可能有效，也可能无效。*

本文面向 JavaScript 经验有限、对该语言理解有限的读者。我将定期为相对较新的 JavaScript 程序员发布文章，也为有经验的程序员发布更高级的文章。如果你用 JavaScript 编码，你可能会想跟我学。

## 1)尽可能避免全局范围

这是我告诉你的个人经验——使用全局范围总是一件痛苦的事情。通常，您通过使用`<script>`标签将 JavaScript 文件放在一个`html`文件中来捆绑它们。这意味着，就好像所有这些文件都写在一个文件中，除非你使用模块或像 Babel 这样的下一代工具。
有很多例子，其中很多都很复杂，难以理解其重要性，但我会为这个简单的场景尽力而为。
假设你想为画布草图存储一些数据，你这样做:

```
let canvasWidth = 5;
let canvasHeight = 67;
let canvasMousePos = { x: 9, y: 6 }; 
```

这可能没问题，但是当您想要为画布添加更多属性时，您将会更加污染全局名称空间。为此，我们使用了`Object`文字。将任何相似的数据存储在单独的全局名称空间中通常是不好的做法，这对于需要扩展的时候也是非常好的。下面的代码显示了一种更好的方法，并且还显示了将来您可能希望包含两个具有不同属性的画布时的情况。

```
let mycanvas = { width: 5, height: 67, mouse: { pos: { x: 9, y: 6 } } };
let myothercanvas = { author: 'John', width: 50, height: 107, mouse: { pos: { x: 9, y: 6, z: 89 } } }; 
```

如果你正在注意，你可能会建议使用一个`Array`来存储这两个画布，以进一步消除全球范围的污染，这正是你应该做的。在某种程度上，你可能想开始用面向对象编程来解决这些问题。

## [T1】2)不要省略分号](#2-do-not-omit-semi-colons)

许多新手和业余开发人员喜欢 JavaScript 的一个特性是，在大多数情况下，分号(；)是可选的，代码运行时没有它们。这个特性被称为自动分号插入。然而，不推荐使用这个特性，你应该在中包含任何到期分号，因为这可能会导致意想不到的行为。分号对于更好的可读性也是至关重要的。

## 3) JavaScript 数组方法

我们将列表存储在数组中，一般来说，这样做是为了在代码中的某一点迭代它们，或者对这些数据进行一些顺序操作。数组方法在这里非常有用。
假设我有一个由`users`组成的数组，每个人都有一个名字和一些与之相关的属性。如果我只想要这些用户的名字，我可以创建一个新的数组，循环遍历原始数组，然后像第一个代码片段一样将项目推入这个新数组，或者我们可以只在数组上使用`map`方法。

```
 const users = [{ name: 'John', age: 87, post: 'high' }, { name: 'Doe', age: 43, post: 'low' }];
    const names = [];
    for(let i = 0; i < users.length; i++) {
        names.push(users[i].name);
    }
    // alternatively, in one line
    const names = users.map(user => user.name); //["John", "Doe"] 
```

有关所有数组方法及其用例的列表，请单击此处的[按钮](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)

## 4)复用任何一段代码？让它成为一种功能

您编写了一些功能，但不得不在其他地方再次使用它，这种情况有多常见？我想这通常是每个人的答案。每当你看到自己重用一段代码，哪怕只是两次，就把它做成一个函数。这意味着如果你想在将来改变代码的工作方式(相信我，这总是会发生的)，你只需要在一个地方改变它，而不是在 5 个 JavaScript 文件中的 23 个地方。

## 尽可能使你的代码简洁

JavaScript 代码允许语法上的灵活性。以至于有可能通过省略语法的冗余部分来使代码块非常简洁，而不会破坏推荐的模式。例如，下面省略了多余的花括号。

```
if (this.isHungry()) this.stroll()
else if (inPerception.length <= 2) this.stroll(() => this.inPerception > 2);
else {
    this.cohesion(inPerception, cohesionPriority / 100);
    const colliding = peerTree.retrieveFromRadius(pos.x, pos.y, r, this.isOfTypeSelf);
    this.seperate(colliding, seperationPriority / 100);
} 
```

*这段代码来自一个项目，该项目模拟了智能自主代理的进化
这个特性必须非常小心地使用，并且只在表达式后面的语句是单数的情况下使用。通常只在必须封装多条语句的地方使用花括号，否则，为了更好的可读性和减少冗余，可以省略花括号。

# 结论

您可能已经注意到，我特别强调了这样一个事实，即代码应该在未来易于扩展，并且对于任何人(不仅仅是您)来说都应该易于查看和理解。这是程序员遵循的关键概念之一。如果你对新程序员有更多的建议，请在下面的评论中留下。如果你读到这里，你应该很想留下一段爱情，看看那会让我们两个多么幸福；)