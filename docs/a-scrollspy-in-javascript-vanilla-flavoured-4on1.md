# JavaScript 中的 scrollspy 香草味

> 原文：<https://dev.to/calebpitan/a-scrollspy-in-javascript-vanilla-flavoured-4on1>

许多开发人员认为，嵌入在第三方代码中的功能对于任何普通开发人员来说都绝对是一件糟糕的事情。我告诉你这是一个大大的“不！”。

不过有一件事仍然成立；第三方代码可能是由不同开发人员合作编写的，因此应该是结构良好的。

尽管如此，为 UI/UX **创建功能部件并不困难**。一个你应该知道的秘密；**“构建一个功能部件是个问题”**。*你会说我自相矛盾——是的，我也这么认为。*

把它当成一个问题，就像每一个计算机问题一样，需要一个解决方案。这就是算法在 UI/UX 设计中发挥作用的地方。

> **注意:** *这里的*功能部件*，我指的是一个对用户交互有脚本反应的 UI 部件。*

## 理解问题。

我们有一个章节索引，可以在目录中找到。我们的问题是:我们希望更新用户已经滚动到 TOC 中的哪个部分。从高处看，这是一个大问题。
直到我们创造出一个模型！

## 创建模型

创建一个模型将我们从如此高的角度，从一个沮丧的位置，移动到与问题相同的平面。现在我们可以利用！创建模型时，我们需要知道我们有什么，并挑选出有用的。我们有 JavaScript。什么会有用？

*   滚动事件。
*   一个滚动位置(`scrollTop`)。
*   每一节距离页面偏移量的距离(`offsetTop`)。
*   构成该部分的元素；(`HTMLHeadingElement`)。我会使用`h2-h4`。

现在我们需要知道在某一特定时刻`scrollTop`何时大于或等于其中一个标题的`offsetTop`。

## 用暗语说话

我们选择`h2-h4`,是因为我们认为`h1`是老大哥标题或上级标题，而`h5-h6`是下级标题，或者更好地说，不如补一节重要。

> **注:**我会尽可能的跟 es5(做事方式)走，只要我认为有必要。只是因为它更传统，我想是这样的——ECMAScript(5x 6)

```
(function() {
  const h2 = document.querySelectorAll("h2")
  const h3 = document.querySelectorAll("h3")
  const h4 = document.querySelectorAll("h4")
}()); 
```

Enter fullscreen mode Exit fullscreen mode

我们还没有完成这里，我已经注意到一个问题。**我们如何将三个标题合并在一起**。记住它们中的每一个都是一个`NodeList`，因此是可迭代的(不一定是[迭代协议](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/iteration_protocols)，甚至是每个常规的`for...`循环)。更像是有一个数组。

因为我们需要将它们放在一起，所以没有比数组更好的地方了！这意味着它们就像我们集合中的一个子数组——你可以称之为多维数组。

```
(function() {
  const h2 = document.querySelectorAll("h2")
  const h3 = document.querySelectorAll("h3")
  const h4 = document.querySelectorAll("h4")
  let h = [h2, h3, h4]
}()); 
```

Enter fullscreen mode Exit fullscreen mode

然而，其他一些问题，我们需要将每个`NodeList`分散到数组中，这样我们就可以有一个线性数组，而且我们也失去了排序。标题元素的顺序不能与它们在定义它们的 HTML 文档中出现的顺序相同，因为它们没有公共的选择器。我们可能有:

```
<h2>Heading 2</h2>
<p>This is a paragraph in a section...</p>

<h3>Heading 3</h3>
<p>This is a paragraph in another section...</p>

<h2>Heading 2</h2>
<p>This is a paragraph in, even, another section...</p> 
```

Enter fullscreen mode Exit fullscreen mode

如果它们都是`h2`，那么它们也将按照相对于它们的`offsetTop`的正确顺序被选择。但是由于在`h2`中有一个`h3`，我们将拥有相对于它们的`offsetTop`没有排序的元素。

我们能想到的一个解决方案是:

1.  循环遍历所有元素并给它们一个公共类名，然后使用该类名重新选择，
2.  或者获取`offsetTop`并排序。出于一些我不知道的原因，我更喜欢这样

为了扩展从每个选定元素返回的`NodeList`,我们将展平数组。`Array.prototype.flat`或者 es6 对象传播`...`就足够了，但是让我们对它进行原始编码。

```
const flatten = function flatten(arr) {
  const reduce = Array.prototype.reduce
  return reduce.call(arr, function(acc, val) {
    return Array.isArray(val) || typeof val[Symbol.iterator] === "function" ? acc.concat(flatten(val)) : acc.concat(val);
  }, [])
} 
```

Enter fullscreen mode Exit fullscreen mode

`arr`参数可能不是数组，但却是可迭代的，因此没有`reduce`方法。所以我们不直接使用`arr.reduce`，而是调用这个方法并给它一个`thisArg`作为它需要引用
的`this`的值

```
(function() {
  const h2 = document.querySelectorAll("h2")
  const h3 = document.querySelectorAll("h3")
  const h4 = document.querySelectorAll("h4")
  let h = flatten([h2, h3, h4])
}()); 
```

Enter fullscreen mode Exit fullscreen mode

## 方案 1

添加一个通用类名并重新选择。可能会有一个初始偏移，可能是因为你的粘性导航条占用了空间

```
(function(offset) {
  const elOffsetIndex = {}
  const h2 = document.querySelectorAll("h2")
  const h3 = document.querySelectorAll("h3")
  const h4 = document.querySelectorAll("h4")
  let h = flatten([h2, h3, h4])

  // Time Complexity: O(n) => O(h.length)
  h.forEach(function(el) {
    el.className = "some-section"
  })

  h = document.querySelectorAll(".some-section")
  // now order is being kept

  window.addEventListener("DOMContentLoaded", function() {
    // without this event, the `offsetTop` value may not be right
    // as document may not have finished rendering
    const offsets = []

    // Time Complexity: O(n) => O(h.length)
    for (var i = 0; i < h.length; i++) {
      let hOffset = h[i].offsetTop + offset;
      offsets.push(hOffset);
      elOffsetIndex[hOffset] = h[i];
    }

    document.addEventListener("scroll", function() {
      const scrollTop = this.documentElement.scrollTop

      // Time Complexity: worst-case O(n) => O(offsets.length)
      for (var i in offsets) {
        if (scrollTop >= offsets[i]) {
          elOffsetIndex[offsets[i]].classList.add("active")
          break
        }
      }
    })
}(0)); 
```

Enter fullscreen mode Exit fullscreen mode

在最坏的情况下，使用大 O 的上述总时间复杂度是`O(3n)`

## 方案二

对标题的`offsetTop`进行排序。我们将使用一个[快速排序](https://en.wikipedia.org/wiki/QuickSort)算法来排序我们的偏移量数组。快速排序的最佳/平均性能为`O(n log n)`，最差性能为`O(n<sup>2</sup>)`。
通过一些优化，我们的排序永远不会达到最坏的情况，因为我们不会遇到任何重复的数字，这意味着没有一个部分放在另一个之上。

### 快速排序

```
const quickSort = function quickSort(data) { // no optimizations
  const partition = function partition(data, lo, hi) {
  const pivot = data[hi]
  let i = lo
  for (let j = lo; j < hi; j++) {
    if (data[j] < pivot) {
      data[i] = data[j] - data[i] + (data[j] = data[i]);
      i++
    }
  }
  // swap
  data[i] = data[hi] - data[i] + (data[hi] = data[i]);
    return i
  };
  const sort = function sort(data, lo, hi) {
    if (lo < hi) {
      let p = partition(data, lo, hi)
      sort(data, lo, p - 1)
      sort(data, p + 1, hi)
    }
  };
  sort(data, 0, data.length - 1)
} 
```

Enter fullscreen mode Exit fullscreen mode

```
(function(offset) {
  const elOffsetIndex = {}
  const h2 = document.querySelectorAll("h2")
  const h3 = document.querySelectorAll("h3")
  const h4 = document.querySelectorAll("h4")
  let h = flatten([h2, h3, h4])

  window.addEventListener("DOMContentLoaded", function() {
    // without this event, the `offsetTop` value may not be right
    // as document may not have finished rendering
    const offsets = []

    // Time Complexity: O(n) => O(h.length)
    for (var i = 0; i < h.length; i++) {
      let hOffset = h[i].offsetTop + offset;
      offsets.push(hOffset);
      elOffsetIndex[hOffset] = h[i];
    }

    // Time Complexity: O(n log(n)) => O(h.length log(h.length))
    quickSort(offsets)

    document.addEventListener("scroll", function() {
      const scrollTop = this.documentElement.scrollTop

      // Time Complexity: worst case O(n) => O(offsets.length)
      for (var i in offsets) {
        if (scrollTop >= offsets[i]) {
          elOffsetIndex[offsets[i]].classList.add("active")
          break
        }
      }
    })
}(0)); 
```

Enter fullscreen mode Exit fullscreen mode

> **注:**所有`log`都在`log<sub>2</sub>`中

使用大 O，上述的总时间复杂度在最坏的情况下是`O(2n + n log(n))`并且很少是`O(2n + n<sup>2</sup>)`。如果很少保持很少，可能有一些优化或没有已经排序(排序)的偏移量，那么这种方式更有效，否则...谢谢大家！