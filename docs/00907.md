# JavaScript 数据结构和算法:数组

> 原文：<https://dev.to/zchtodd/javascript-data-structures-algorithms-arrays-pnp>

网上有很多地方可以了解数组。我想用一种不同的方式来展示数组的基础知识。让我们来快速浏览一下 JavaScript 数组函数，并想象一下它们是如何工作的。

对于每个功能，我们将看一个示例动画。

记住，如果你想看不止一遍，你可以点击每个动画底部的重新运行！

### 推送

这可能是最简单的方法之一，也是你会经常看到的方法。push 方法将一个项目附加到数组的末尾。一个项可以是任何东西:数字、字符串、对象、另一个数组。

JavaScript 数组的大小会随着您将项目放入其中而增加。您不需要提前做任何事情来将项目推送到您的数组中，不像在其他一些语言(如 C)中，您需要为项目分配内存。

推送方法是所谓的“就地”方法。该数组被“就地”更改，因此如果您再次引用该数组，它将包含您的新项。其他方法可以完全返回一个新数组，同时保持原始数组不变。

[https://codepen.io/zchtodd/embed/QWLzQVv?height=600&default-tab=result&embed-version=2](https://codepen.io/zchtodd/embed/QWLzQVv?height=600&default-tab=result&embed-version=2)

### 流行

你可以认为 pop 是 push 的反义词。当你从一个数组中弹出时，不是在末尾添加一些东西，而是已经在末尾的东西从数组中弹出。弹出的项也从 pop 方法返回，因此您可以在变量中存储对该项的引用。

就像 push 方法一样，这是一个修改原始数组的就地操作。

如果从空数组中弹出，你认为会发生什么？至少在 JavaScript 中，数组保持为空，返回值为 undefined。在其他语言中，比如 Python，这将是一个错误。

[https://codepen.io/zchtodd/embed/BaBvrxJ?height=600&default-tab=result&embed-version=2](https://codepen.io/zchtodd/embed/BaBvrxJ?height=600&default-tab=result&embed-version=2)

### 轮班

如果你想从数组的开始而不是末尾删除一些东西，该怎么办呢？转移到救援！Shift 的行为类似于 pop，但它移除并返回数组的第一项。同样像 pop 一样，从空数组转移会返回 undefined。

[https://codepen.io/zchtodd/embed/KKPbojJ?height=600&default-tab=result&embed-version=2](https://codepen.io/zchtodd/embed/KKPbojJ?height=600&default-tab=result&embed-version=2)

### 未移位

这里我们有一个很像 append 的方法，但是它不是添加到数组的末尾，而是添加到前面！数组中的所有元素都被移动，为最新的元素腾出空间，因此先前位于索引 0 的元素现在将位于索引 1。

作为一个额外的奖励，unshift 将在添加元素后返回数组的新长度！

[https://codepen.io/zchtodd/embed/VwZqxez?height=600&default-tab=result&embed-version=2](https://codepen.io/zchtodd/embed/VwZqxez?height=600&default-tab=result&embed-version=2)

### 拼接

这是一种增压方法:它负责在特定索引**处插入和移除元素，并在**处替换元素。使用这种方法有很多不同的方法。

您可以从给定的索引开始插入或移除一个或多个元素，也可以移除索引之后的所有元素。数组会现地变更，但是 splice 也会传回包含任何已删除元素的新数组。

[https://codepen.io/zchtodd/embed/KKPbRXP?height=600&default-tab=result&embed-version=2](https://codepen.io/zchtodd/embed/KKPbRXP?height=600&default-tab=result&embed-version=2)

我希望你喜欢这些动画！如果您想看到其他 JavaScript 数据结构的动画，请告诉我。