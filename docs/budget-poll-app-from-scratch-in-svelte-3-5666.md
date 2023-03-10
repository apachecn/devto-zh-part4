# 预算调查应用程序从零开始在苗条 3

> 原文：<https://dev.to/corvusetiam/budget-poll-app-from-scratch-in-svelte-3-5666>

欢迎阅读我们的第三部分。我终于把整个项目上传到了 GitHub。您可以克隆它并在您的机器上测试。

为了完成这个项目，做了哪些改变？

我们缺少一个组件:平衡页面，关于我们的小钱投票的最终数据。其次，我发现，稍微清理一下会让整个经历变得更好。
而且我们一路上几乎没有遇到什么问题。我会试着向你解释这些变化以及我为什么要做这些改变。尽管如此，这是**初学者**水平的项目，我们没有很多机会在代码中使用复杂的模式。

我写了一点 CSS 来使我们的项目更具可读性。它仍然缺少一些细节，但我将不得不接受它。我会修复它们，但目前整个事情运作良好。

## 余额页面

您应该从名为`Balance.svelte`的新文件开始。
会超级简单。

```
<div>
    <h2>Poll Table Balance</h2>
    <table>
        <thead>
            <tr>
                <th>No.</th>
                <th>Person</th>
                <th>Paid</th>
                <th>Balance</th>
            </tr>
        </thead>
        <tbody>
            {#each $pollState.people as person, index }
            <tr>
                <td>{index + 1}.</td>
                <td>{person.name}</td>
                <td>{ format_currency(currency, person.amount) }</td>
                <td>{ format_currency(currency, person.amount - average_amount ) }</td>
            </tr>
            {/each}
        </tbody>
    </table>
</div>
<div>
    <h2>Summary</h2>
    <div>
        <h3>Person Polling money</h3>
        <p>{ $pollState.people.length }</p>    
    </div>
    <div>
        <h3>Average amount per person</h3>
        <p>{ format_currency($pollState.poll.currency, average_amount) }</p>    
    </div>
    <div>
        <h3>Money owed to other person</h3>
        <p>{ format_currency($pollState.poll.currency, compute_owed_money()) }</p>
    </div>
</div> 
```

前面已经解释了所有的苗条身材。只有两个新零件。

变量`average_amount`和函数`compute_owed_money`。
我们在模板- `compute_owed_money()`中使用了函数调用，由于一些小细节，我们可以顺利完成。通常情况下，我会把它放入反应变量中，让它一直工作。

这一次我采用了简单的方法。为什么？你可能会问，什么是如此*简单化的*或*非最优的*。好吧，首先。

当呈现模板代码时，里面的函数只计算一次。我的意思是，如果我们在状态改变时不强制重新加载模板，模板将保持不变。

```
<script>

    let arr = [1, 2, 3, 4];

    function test() {
        return arr[Math.floor(( arr.length ) * Math.random())];
    }

    function update() {     
        arr.push(arr.length); 
        arr = arr;      
    }
</script>
<div>
    <p>Random value is: {test()}</p>
        <button type="button" on:click={ (ev) => {  update(); } }>Click me</button>
    <ul>
    {#each arr as item}
        <li>{item}</li>
    {/each}
    </ul>
</div> 
```

这是一个很小的例子。测试函数将从我们的数组`arr`中选取随机值。
函数`update`将等于数组大小的新值推入`arr`。并把它重新分配给它自己，以迫使苗条的反应行为。

它将更新我们对`<li>{item}</li>`
的列表渲染，但是`{test()}`中的值是什么呢？即使我们的数组改变了大小，它还会相等吗？

测试一下。你可以使用由 [svelte.dev](https://svelte.dev/) 提供的 REPL。
为什么我们的列表会改变，而我们的`test()`不会，而且它只渲染一次？

现在让我改变一件事。

```
<script>
    let visible = true;     
    let arr = [1, 2, 3, 4];

    function test() {
        return arr[Math.floor(( arr.length ) * Math.random())];
    }

    function update() {     
        arr.push(arr.length); 
        arr = arr;      
    }
</script>
<div>
    {#if visible}
    <p>Random value is: {test()}</p>
        <button type="button" on:click={ (ev) => {  update(); } }>Click me</button>
    <ul>
    {#each arr as item}
        <li>{item}</li>
    {/each}
    </ul>
    {/if}
    <button type="button" on:click={ev => { visible = !visible; }}>Hide and Show</button>
</div> 
```

试试看，试着点击几次`hide and show`按钮，然后再点击几次`Click me`按钮。你知道为什么会这样吗？这是因为，当我们用`{#if ...}`隐藏部分模板或者用`{#each}`渲染或者用`{#await}`渲染时，当变量改变时，我们会强制更新模板。

或者模板的全部内容。

那么如何实现这样的东西呢？如何更新我们的`test()`值或欠款。
为什么，我们的`compute_owed_money()`管用？

首先，最简单的方法是添加额外的变量并在`update()`中赋予它我们的`test()`结果。

就`computed_owed_money()`而言，我们负责更新面板内容的`{#if }`包装器在`FormPanel.svelte`内部。

是的，这些组件像树一样更新。从顶部`App.svelte`到底部`Balance.svelte`。

为了不用这个`{#if ...}`来计算我们欠的钱，例如当你想要纯 CSS 的 hide-and-show 时，最简单的方法就是直接使用 stores API。

记住那些标志是如何正确工作的。现在，是时候来点儿啤酒，丢几块钱了。

```
 import { pollStore } from "./globals.js";

$: average_amount = ($pollState.poll.amount / $pollState.people.length);

function compute_owed_money(people) {
    let acc = 0;
    for ( let i = 0; i < people.length; i++ ) {
        let diff = (average_amount - people[i].amount);
        if ( diff > 0 ) {
            acc += diff 
        }
    }

    return acc;
}

let owed_money = compute_owed_money($pollStore.people);

let unsub_owed = pollStore.subscribe(store => {
    owed_money = compute_owed_money(store.people);
}) 
```

开始了。我们通常使用隐藏在句法糖后面的东西。

你可以在 svelte.dev store API docs 上了解更多关于可写的内容。

每次我们的存储发生变化时，它都会发出对 pollStore.subscribe 内部函数的调用，并将存储值作为参数传递给该调用。

## 快速说说 CSS

我不打算把所有的 CSS 都放在这里。实际上重要的是，你可以免费获得 Svelte3，CSS 模块。它会自动添加具有类似散列名称的特殊类，并在单个包中生成适当的 CSS 代码。

## 总结

我从这个项目中学到了很多，并对 Svelte3 带给你的东西获得了相当大的尊重:

*   自由的
*   更快的
*   而且不用安装我个人最讨厌的 webpack。

我的部件小，速度快。整个代码感觉像是用几乎原始的 JS 编写的，没有使用任何编译器/框架/unicorn。

尽管如此，这仍然是一个非常初级的项目，我喜欢在更大的东西上尝试苗条。

令我印象深刻的是，最终尺寸非常小。整个 javascript 包重约 60kB，即使启用了开发工具也没有缩小。

对默认给定的一切作出反应，开发-构建中的全尺寸加权 5 倍以上。可能尺寸不大，比大多数图片都小。不同之处在于:图像可以在屏幕上快速渲染。JS 可以运行任何计算。它们都需要时间/处理器/加热你的手机，你们两个都在小村庄的某个地方倒一些草皮，比必要的多一点愤怒，你给气候变化增加了一点沙子。

苗条的感觉更好。我想我会尝试在 Vue 中重新实现这个或一些类似的项目。我有更多的想法可以尝试。

你觉得这个系列怎么样？你喜欢吃吗?什么东西太难理解或者写得很奇怪？你会改变什么？我很想收到你的来信。

再见，祝您愉快。