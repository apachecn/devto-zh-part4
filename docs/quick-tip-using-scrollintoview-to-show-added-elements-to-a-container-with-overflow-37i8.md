# 快速提示:使用 scrollIntoView()显示添加到溢出容器中的元素

> 原文：<https://dev.to/codepo8/quick-tip-using-scrollintoview-to-show-added-elements-to-a-container-with-overflow-37i8>

我们经常希望有一个界面，在那里我们有一个溢出的容器:带有页眉和页脚的滚动。

这样做的一个问题是，当我们向容器中添加新元素时，它们会被添加到容器的末尾，但是在用户向下滚动之前，元素不会显示出来。

过去，我们通过比较`scrollHeight`和`clientHeight`并设置元素的`scrollTop`属性来解决这个问题。这很容易出错，而且看起来过于复杂。

输入 [scrollIntoView()](https://developer.mozilla.org/en-US/docs/Web/API/Element/scrollIntoView) 。使用这个 API 确保向用户显示一个新元素就像在元素本身上调用它一样简单。

假设我们有下面的字符列表和一个添加更多字符的按钮。

```
<div class="scrollbody">
    <ul>
        <li>Billy Butcher</li>
        <li>Hughie Campbell</li>
        <li>Mother's Milk</li>
        <li>The Frenchman</li>
  </ul>
</div> 
<button>Add new character</button> 
```

在 CSS 中，我们限制主体滚动，并将其设置为溢出。

```
.scrollbody {
  height: 6em;
  overflow: scroll;
} 
```

通过一点 JavaScript，我们现在可以在每次点击按钮时添加新的字符:

```
let characters = ['The Female (of the Species)',
'Lieutenant Colonel Greg D. Mallory','The Legend','The Homelander',
'Black Noir','Queen Maeve','A-Train','The Deep','Jack from Jupiter',
'The Lamplighter','Starlight','Mister Marathon','Big Game',
'DogKnott','PopClaw','Blarney Cock','Whack Job',
'Gunpowder','Shout Out'];

let count = 0;
document.querySelector('button').addEventListener('click', (ev) => {
    if (count < characters.length) {
    let item = document.createElement('li');
    item.innerText = characters[count];
    document.querySelector('ul').appendChild(item);
    count = count + 1;
  }
  ev.preventDefault;
}); 
```

现在，如果您尝试这样做，您会看到新的字符实际上是添加的，但是直到您向下滚动到它们时才可见。

[https://codepen.io/codepo8/embed/xvarxB?height=600&default-tab=result&embed-version=2](https://codepen.io/codepo8/embed/xvarxB?height=600&default-tab=result&embed-version=2)

[![elements not showing until the user scrolls](img/0e00c13df126e7119daffdff2e0115a6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2TFJP91Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://christianheilmann.com/wp-content/uploads/2019/08/without-scrollintoview.gif)

那很糟糕。要解决这个问题，你需要做的就是在添加新的列表项之后添加 scrollIntoView 调用:

```
/* list of characters */
let count = 0;
document.querySelector('button').addEventListener('click', (ev) => {
    if (count < characters.length) {
    let item = document.createElement('li');
    item.innerText = characters[count];
    document.querySelector('ul').appendChild(item);

    item.scrollIntoView();

    count = count + 1;
  }
  ev.preventDefault;
}); 
```

[https://codepen.io/codepo8/embed/RXYgJK?height=600&default-tab=result&embed-version=2](https://codepen.io/codepo8/embed/RXYgJK?height=600&default-tab=result&embed-version=2)

[![scrollintoview showing the new elements](img/369ae064921969b0cc5f30f65d306e26.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3ZRWekbO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://christianheilmann.com/wp-content/uploads/2019/08/scrollintoview-without-smooth.gif)

作为一个额外的奖励，你可以定义滚动的行为是平滑的，这使得滚动缓慢而不刺耳:

```
/* list of characters */
let count = 0;
document.querySelector('button').addEventListener('click', (ev) => {
    if (count < characters.length) {
    let item = document.createElement('li');
    item.innerText = characters[count];
    document.querySelector('ul').appendChild(item);

    item.scrollIntoView({behavior: 'smooth'});

    count = count + 1;
  }
  ev.preventDefault;
}); 
```

[https://codepen.io/codepo8/embed/wVEeQm?height=600&default-tab=result&embed-version=2](https://codepen.io/codepo8/embed/wVEeQm?height=600&default-tab=result&embed-version=2)

[![scrollintoview showing the new elements smoothly](img/723e9c97fa15e62ecd463a82cfbae43c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--m4e7bwgJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://christianheilmann.com/wp-content/uploads/2019/08/scrollintoview-with-smooth.gif)

另一个提醒是，要继续研究标准中的内容，而不是在 StackOverFlow 上找到一个旧的解决方案，并保持过于复杂和未经浏览器优化的代码。