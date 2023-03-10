# 在 Vue 应用程序中使用键盘

> 原文：<https://dev.to/raymondcamden/working-with-the-keyboard-in-your-vue-app-30d3>

这个周末我开始在 Vue.js 开发另一款游戏(如果你好奇，你可以在这里[看一看](https://taipan.raymondcamden.now.sh/))。对于游戏的一部分，我想真正利用键盘进行互动。我的目标是，我不会 100%实现，一款你可以在玩游戏的整个过程中使用键盘的游戏。我知道 JavaScript 可以访问键盘事件，但我从未尝试在 Vue 中使用它们。在我分享我的发现之前，我想对 Vue 论坛的 [LinusBorg](https://forum.vuejs.org/u/LinusBorg) 大喊一声。下面的好东西都是他的，不好的东西和错误都是我的错。

好，让我们从一个简单的例子开始。如果你查看事件处理的 Vue 文档，你会发现有一个专门的章节谈到了[按键修饰符](https://vuejs.org/v2/guide/events.html#Key-Modifiers)。本节讨论如何添加快捷键来监听特定的键。虽然这并不完全是我想要的，但它让我确信使用键盘将会很容易。例如，这将在每次`keyup`调用时触发一个事件:

```
<input @keyup="keyEvent"> 
```

Enter fullscreen mode Exit fullscreen mode

该修改只有在按下回车键时才会触发:

```
<input @keyup.enter="keyEvent"> 
```

Enter fullscreen mode Exit fullscreen mode

酷！但是请注意事件是如何绑定到输入字段的。根据我的需要，我希望在“应用程序”级别进行键盘处理，也就是说不需要首先使用输入字段。考虑这个例子。

```
<div id="app" v-cloak @keyup.enter="test('div enter', $event)" @keyup="test('div',$event)">
  <input @keyup="test('input', $event)"><br/>
  enter only: <input @keyup.enter="test('second input', $event)">
</div> 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我有`keyup`的多种用法。我将一个标签传递给我的测试处理程序和`$event`对象。我在`div`级别听了两次，然后对每个输入字段听了一次。我的处理程序只是回显传入的内容:

```
test(where, e) {
    console.log(`keyuptest at ${where} with code ${e.keyCode}`);
} 
```

Enter fullscreen mode Exit fullscreen mode

结果很有趣。如果您在任何输入字段之外键入，则不会注册任何内容。但是，如果您首先单击两个输入字段中的一个，事情会像预期的那样工作。输入处理程序和 div 处理程序都将被触发。这个你可以在我的 [Codepen](https://codepen.io/cfjedimaster/pen/rXbywY?editors=1111) 自己测试。

所以再多搜索一下，我看到了这个 Vue.js 论坛的帖子:[捕捉所有键的按键](https://forum.vuejs.org/t/capture-keypress-for-all-keys/14560)。在其中，海报询问如何响应应用程序中的任何和所有按键事件。LinusBorg 想出了一个简单的解决方案，归结起来就是:

```
mounted() {
    window.addEventListener("keypress", e => {
        console.log(String.fromCharCode(e.keyCode));
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

在我的测试中，这非常有效，但是我遇到了一个有趣的问题。我的游戏利用了路由，我只需要监听一个路由中的键盘事件。当我离开这条路线并返回时，事件侦听器会再次被绑定。我做得越多，绑定到`keypress`的重复事件处理程序就越多。

我在这个问题上又挣扎了一会儿，LinusBorg 又想出了一个解决办法。我知道`window.removeEventListener`，但是它对匿名函数不起作用。解决方案是只使用 Vue 方法来注册和删除事件。这可能没有意义，但这里有一个简单的例子:

```
created() {
    window.addEventListener('keypress', this.doCommand);
},
destroyed() {
    window.removeEventListener('keypress', this.doCommand);
},
methods: {
    doCommand(e) {
        let cmd = String.fromCharCode(e.keyCode).toLowerCase();
        // do stuff
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！当然，在我的游戏中事情有点复杂，但是我会把这些留给描述我游戏的文章。一如既往，我希望这有所帮助！

*un splash 上 [Csabi Elter](https://unsplash.com/@bulgakovmihaly?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 的标题照片*