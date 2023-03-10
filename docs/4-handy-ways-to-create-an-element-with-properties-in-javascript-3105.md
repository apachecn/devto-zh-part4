# 用 Javascript 创建具有属性的元素的 4 种简便方法

> 原文：<https://dev.to/felipperegazio/4-handy-ways-to-create-an-element-with-properties-in-javascript-3105>

下面是使用 Javascript 创建具有某些属性的元素的一些不同方法。您可以将它们全部直接粘贴到控制台中。有些比其他的更方便，但是这篇文章的目的主要是为了好玩。

# 旧派

```
let elem1 = document.createElement('div');
elem1.id = 'fizz'; 
```

Enter fullscreen mode Exit fullscreen mode

# 最酷

我在 dev.to 和 Sam Thorogood 上了解到这一点，现在我还没有找到最初的帖子(我不记得在哪里，但我知道我在他的帖子上看到过)。谢谢萨姆。

```
let elem2 = Object.assign(document.createElement('div'), {
  id: 'fizz'
}); 
```

Enter fullscreen mode Exit fullscreen mode

# 价格昂贵

我认为当你想创建一个有很多子元素的复杂的元素树，并且确信一切正常时，这是非常有用的。

```
let html = '<div id="fizz"></div>';
let elem3 = new DOMParser().parseFromString(html, 'text/html').body.firstChild; 
```

Enter fullscreen mode Exit fullscreen mode

# 丑陋

```
let elem4 = document.createElement('div');
for (var [key, value] of Object.entries({id:'fizz'})) {
  elem4[key] = value;
} 
```

Enter fullscreen mode Exit fullscreen mode

我没有检查这些代码的兼容性等所有问题，但我认为无论如何知道这些是件好事。谷歌或朋友肯定可以帮助解决适用性问题。如果你知道其他一些很酷的方法，请分享:)