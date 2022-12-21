# 将异步/等待与回调一起使用

> 原文：<https://dev.to/megazear7/using-async-await-with-callbacks-28o4>

我最近遇到了让 promise 接口与回调接口一起工作的问题。具体来说，我有一组承诺，并希望用 JavaScript 的`forEach`方法迭代这些值。正如我们将看到的，这是有问题的，然而解决方案是现成的。我花了一段时间才明白发生了什么，但希望你能跳过我的痛苦，因为我会在这里解释这一切。

> 声明:这篇博文中的代码被减少以突出问题的根源。这不是在 JavaScript 中实现该功能的最佳方式。相反，它意味着使问题和解决方案变得清晰。

## 一个没有承诺的起点

我从一组值开始。我在这个数组上执行了一些逻辑，将值合并成一个值，然后返回结果。

```
function getMovieNames() {
  let displayToUser = '';
  let movieNames = [
    "Lord of the Rings",
    "Band of Brothers",
    "Interstellar"
  ];

  movieNames.forEach(movieName =>
    displayToUser += movieName + '  ');

  return displayToUser;
}

alert(getMovieNames()); 
```

Enter fullscreen mode Exit fullscreen mode

这很简单:它提醒一个电影名称的组合字符串。目前没有问题。

## 一诺千金更新

由于手头问题之外的原因，我不得不获取我正在接收的数据，并用它获取其他数据。现在，我们不再使用值数组，而是遍历这些值并检索异步数据。你能猜出 get 返回了什么吗？

```
function getReleaseDates() {
  let displayToUser = '';
  let movieNames = [
    "Lord of the Rings",
    "Band of Brothers",
    "Interstellar"
  ];

  movieNames.forEach(movieName =>
    displayToUser += fetchReleaseDate(movieName));

  return displayToUser;
}

alert(getReleaseDates()); 
```

Enter fullscreen mode Exit fullscreen mode

空字符串！当然，如果没有看到`fetchReleaseDate`的实现，你是无法知道的，但是如果我们假设它正在发出一个 HTTP 请求或者采取一些类似的异步动作，那么在`forEach`回调被执行之前`getReleaseDates`就会被返回。

## 恭候救援

如果我们需要等待`fetchReleaseDate`方法，似乎 await 关键字应该可以做到这一点。对吗？为了等待`fetchReleaseDate`，它所在的函数需要被标记为 async。在这种情况下，这就是我们给`forEach`方法的回调。

```
async function getReleaseDates() {
  let displayToUser = '';
  let movieNames = [
    "Lord of the Rings",
    "Band of Brothers",
    "Interstellar"
  ];

  movieNames.forEach(async movieName =>
    displayToUser += await fetchReleaseDate(movieName));

  return displayToUser;
}

alert(await getReleaseDates()); 
```

Enter fullscreen mode Exit fullscreen mode

没那么快！即使我们等待`fetchReleaseDate`方法，`forEach`方法也不会等待我们的回调。所以结果和以前没什么不同。

## 为援救之意

如果问题是没有等待回调，解决方法是删除回调。要做到这一点，让我们用一个`for...of`块来改变`forEach`的使用，如下所示。

```
async function getReleaseDates() {
  let displayToUser = '';
  let movieNames = [
    "Lord of the Rings",
    "Band of Brothers",
    "Interstellar"
  ];

  for (movieName of movieNames) {
    displayToUser += await fetchReleaseDate(movieName);
  }

  return displayToUser;
}

alert(await getReleaseDates()); 
```

Enter fullscreen mode Exit fullscreen mode

终于！又起作用了。虽然这对于一些人来说可能是显而易见的，但是像我一样沉迷于使用`forEach`和`map`风格的数组的其他人可能看不到这种伴随回调使用`async` / `await`的 gotchya。

> 临别提示:如果为了在回调函数中使用 await 关键字而将回调函数声明为 async，首先问问自己:“我调用的函数会在我的回调中等待吗？”如果答案是否定的，那么您将需要重构回调函数的使用。

查看我的博客,了解更多我对技术和其他各种话题的思考。