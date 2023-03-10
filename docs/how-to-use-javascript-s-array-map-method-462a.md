# 如何使用 Javascript 的数组映射方法

> 原文：<https://dev.to/raddevon/how-to-use-javascript-s-array-map-method-462a>

## 它有什么作用

Javascript 的 array `map`方法返回一个转换后的数组。您传递给它一个函数，它将转换数组中的每个值。Map 将对数组中的每一项调用函数，传递三个值:**数组项、该项的索引和整个数组**。无论你的函数返回什么，都将成为新数组中相应的项。

## 举个例子

理解 map 做什么的最好方法是通过一个例子。这里有一个将标题转换为引导文章的函数(“a”、“an”或“the”)。它返回相同的标题，并将文章移动到末尾。如果你有一个标题列表，并想按字母顺序排列，你要确保那些前导文章不会影响顺序。你不会希望所有的标题都以“the”开头，以“t”排序。

```
 function formatTitle(title) {
      const articles = ['a', 'an', 'the'];
      let titleWords = title.split('  ');
      let firstWord = titleWords.shift();
      if (articles.includes(firstWord.toLowerCase())) {
        let formattedTitle = `${titleWords.join('  ')}, ${firstWord}`;
        return formattedTitle;
      } else {
        return title;
      }
    } 
```

Enter fullscreen mode Exit fullscreen mode

### 分解回调函数

让我们来分解这个函数。该函数有一个参数:**一个标题**。我知道 map 除了标题之外还会传递两个以上的参数，但是我不需要那些。我只需要命名我将在函数中用到的参数。其他的仍将被传递，但没有与之相关联的参数名。

在第 2 行，我创建了一个我要查找的文章的数组。后面我要取标题的第一个字**看它是否出现在这个数组**里。它告诉我这个标题是否需要格式化。

第 3 行获取标题并调用传入一个空格的`split`方法。`split`是一个字符串方法，将一个字符串拆分成一个数组。传递给`split`的字符串就是原始字符串被拆分的字符串。这个将在每个空格处被分割成一个字符串中单词的数组。

第 4 行使用`shift`，它从数组*中移除第一个值，然后*返回它。第一个单词现在存储在`firstWord`变量中，并且`titleWords`数组不再包含第一个单词。

在第 5 行，我通过将数组传递给 includes 方法来测试`articles`是否包含小写版本的`firstWord`。每个标题都应该以大写字母开头，但是，通过在比较之前将单词小写，我不必担心这个应用程序的用户是否真的在做正确的事情，并且将他们的标题大写。无论如何，该功能都会正常工作。

如果在`articles`数组中找到了第一个单词，我将构建格式化字符串。我用一个模板字符串来做这件事，首先使用数组的`join`方法重新连接标题中剩余的单词。传递一个只有一个空格的字符串意味着当它们重新连接时，数组中的字符串之间会放置空格。记住，这个数组不包含第一个单词，我们现在已经确定它是一个冠词。

我在第一个单词后面加了一个逗号。然后，我在最后放入第一篇文章(`firstWord`)并返回该值。如果第一个单词*不是*一篇文章(else 情况)，我就返回原来的标题。

学习写代码是很棒的，但是你可能想超越仅仅学习，实际上**通过写代码获得报酬**。我可以帮你实现这个飞跃。在 [Rad Devon](https://raddevon.com/) 注册，获得帮助开始你的网络开发生涯！

### 调用地图

为了使用这个回调，我将调用原始数组上的`map`并传入`formatTitle`函数。这将返回一个带有格式化标题的转换后的数组，而不是原始数组。因为它返回一个新数组，而不是改变原来的数组，所以我可能想用一个变量赋值来捕获它，或者直接把它传递给另一个函数或方法。这是组装成一个工作应用程序后的样子。

## 一个工作演示

这个演示使用了上面显示的回调函数。这是一个简单的 Vue 应用程序，显示 IMDB 上排名前 10 的电影，按字母顺序排列。在第一个列表中，它们按标题排序，没有任何修改。在第二个列表中，我对通过将`formatTitle`传递给 map 生成的新数组进行排序。

[https://codepen.io/raddevon/embed/gJKqqe/?height=600&default-tab=js,result&embed-version=2](https://codepen.io/raddevon/embed/gJKqqe/?height=600&default-tab=js,result&embed-version=2)

## 何时使用地图

也许你需要以一种特殊的方式格式化一个字符串，就像上面的演示一样，或者你需要对一个数字数组中的每个值进行计算。无论哪种情况，`map`都是这项工作的最佳工具。任何时候你需要一个新的数组时都可以使用`map`，其中的**每个值都是现有数组**中一个值的转换。

如果你喜欢这篇文章，你可能也会喜欢我写的关于学习使用 Javascript 三元运算符和[数组的 reduce 方法](https://raddevon.com/articles/how-to-use-javascripts-array-reduce-method/)的文章。