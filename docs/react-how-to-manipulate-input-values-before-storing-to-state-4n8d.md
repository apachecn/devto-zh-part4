# [React]在存储到状态之前如何操作输入值。

> 原文：<https://dev.to/daviddoes/react-how-to-manipulate-input-values-before-storing-to-state-4n8d>

在从事一个项目时，我认为在将输入值存储到状态之前对其进行一些操作是很重要的。也就是说，将用户在该字段中键入的每个单词的首字母大写。

我不确定如何在 JavaScript 中做到这一点，也不确定如何在将 React 组件存储到 state 之前将大写行为实现到 React 组件中(这对大多数人来说似乎是显而易见的，但我已经离开这个项目几周了)。

说了这么多，做了这么多，我意识到我在这方面看到的并不多，所以我在这里与你们分享。

## 我们在哪里做？

如果我们看看我们的组件，我们可能每个表单或者甚至每个字段都有一个组件。在那里，我们有所谓的`handlers`。这些小伙伴接受我们的输入，并用它们做一些事情。很有可能，把它存储到我们的状态。

这个处理程序可能看起来像这样:

```
 handleInput(event, key) {
    this.setState({
      [key]: event.target.value
    });
  } 
```

它所做的是存储输入字段的`value`(无论用户键入或选择什么),并在从我们的`onChange`事件传递的`key`,将它放入这个组件的状态对象。

我们用`onChange`输入的内容可能看起来像这样(这里通常会更多，但是让我们简化一下):

```
 <input
      required
      type="text"
      onChange={event => this.handleInput(event, 'firstName')}
    /> 
```

我们的`onChange`正在运行`handleInput`处理程序，传递`event`值和`firstName`键。

那么，我们的状态可能是这样的:

```
this.state = {
  firstName: '',
  lastName: '',
  age: ''
} 
```

如果我们在`onChange`中传递的内容与状态对象中的一个键不匹配，这将不起作用。

尽管如此，我们在哪里做呢？

我们的`handler`是做这件事的最佳地点。我们将把数据从我们的输入端`onChange`向上传递到我们的`handleInput`。在我们`setState`之前，我们将执行我们的逻辑。

## 我们是怎么做到的？

我自己也不太清楚如何做到这一点，所以我不得不去打猎。我知道我们必须`split``join`和`toUpperCase`做一些事情，但除此之外我并不完全确定。

事实证明，这很简单。

首先，让我们了解一下*我们这样做是为了什么*。简而言之，我们需要操作我们的`event.target.value`，即用户在该字段中输入的任何内容。

下面是我们需要对我们的`event.target.value`输入执行的所有事情，以大写每个单词的第一个字母:

```
 .toLowerCase()
      .split(' ')
      .map(s => s.charAt(0).toUpperCase() + s.substr(1))
      .join(' '); 
```

*   首先是把每个字符都变成小写。
*   `split(' ')`获取原始字符串(`event.target.value`)中的每个字符，并将它们拆分成一个数组，该数组等于该字符串中的每个字符。因此，如果我们的用户提供约翰，我们将得到`[j,o,h,n]`。这使我们能够`map`超过每个字符。
*   `map(s ...)` -在这里，我们使用`s`来表示每次迭代，因此每个字符，这是一个单字符串。
*   `map(s => s.charAt(0) ...)` -如果我们的迭代是数组中第`0`位的字符...
*   然后把它大写...
*   这个更难解释。`substr`(T2 的缩写)返回你定义的索引值处的字符串部分，以及其后的任何内容。在这种情况下，除了第一个索引值。或者，我们可以用它来返回前两个索引`(0, 1)`或`Jo`。我们使用`+`将所有的数组元素组合在一起。

所以我们的代码应该是这样的:

```
 let capsText = event.target.value
      .toLowerCase()
      .split(' ')
      .map(s => s.charAt(0).toUpperCase() + s.substr(1))
      .join(' '); 
```

现在，如果我们`console.log(capsText)`，你会看到我们得到了我们想要的。我们如何把它保存在 state 中？

简单！

```
 this.setState({
      [key]: capsText
    }); 
```

让我们把它们放在一起！

```
 handleInput(event, key) {
    let capsText = event.target.value
      .toLowerCase()
      .split(' ')
      .map(s => s.charAt(0).toUpperCase() + s.substr(1))
      .join(' ');

    this.setState({
      [key]: capsText
    });
  } 
```

就是这样！不算太坏，是吧？

感谢阅读。