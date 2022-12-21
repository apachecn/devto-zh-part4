# 构建苗条 3 预算调查应用程序[2]

> 原文：<https://dev.to/corvusetiam/building-svelte-3-budget-poll-app-2-1cid>

# 我们结束的地方

在我上一篇[帖子](https://dev.to/corvusetiam/how-to-build-budget-poll-app-in-svelte3-13c8)中，我讲述了安装和使用苗条身材的基本知识。我们创建了 git 库，制作了多页面表单组件和面板组件。
现在，我们将尝试设计我们的内容面板。

# 开机

在你最喜欢的编辑器中打开我们的 repo，添加几个文件。我们将需要每个面板的组件。

1.  姓名和全额等初始轮询数据成分-> `PollHeader.svelte`
2.  参加投票的人-> `PollContent.svelte`
3.  包含每个人计算金额的最终表格-> `Balance.svelte`

请现在创建这三个文件。我们还需要在`globals.js`中增加 2 家新商店。
我们将它们命名为:`peopleStore`和`pollStore`。

```
export const peopleStore = writable({
    people: []
})

export const pollStore = writable({
    name: null,
    amount: 0,
    currency: null
}) 
```

现在，我们将导入并使用包含应用程序逻辑`PollHeader`的第一个组件。
里面的`App.svelte`你得导入，就像上次一样既有新的`PollHeader`组件又有新的商店。

包含`App.svelte`的 HTML 模板应该看起来像

```
<FormPanel index={0}>
    <PollHeader />
</FormPanel> 
```

现在让我们设计我们的 PollHeader。

## PollHeader 组件

```
<script></script>
<style>
    .container {
        display: flex;
        flex-direction: column;
    }

    .group {
        display: flex;
        flex-direction: row;
    }

    .group label {
        width: 30%;
        margin-right: auto;
    }

    .group input {
        flex: 1;
    }

    .group select {
        flex: 1;
    }

    .push-right {
        margin-left: auto;
    }
</style>
<fieldset>
    <legend>Poll General Data</legend>
    <p>Please provide name of the poll and amount</p>
    <div class="container">
        <div class="group">
            <label for="pollName">Poll Name: </label>
            <input type="text" id="pollName" required>
        </div>
        <div class="group">
            <label for="pollAmount">Poll Amount: </label>
            <input type="number" id="pollAmount" required>
            <select id="pollCurrency">
                <option value="" selected disabled>Select Currency</option>
            </select>
        </div>
        <div class="group">
            <button class="push-right" type="button">Save</button>
        </div>
    </div>
</fieldset> 
```

首先，我们制作了基本的组件模板。有一点，你可能注意到了，我们在这里再次使用了类`.container`。
我们以前用过。这很重要。Svelte3 可以将你的 CSS 编译成模块，比如用哈希替换普通的类名，使它们更容易操作。更重要的是，你不必担心他们。
如果你想在 svelte 中使用一些全局 css 也是可能的。只需在`public/global.css`中定义你的 css 并像其他 css 文件一样使用。

现在，我不想在 CSS 上花太多时间。你可以在这里阅读:

*   FlexBox 的 CSS 技巧指南-[https://css-tricks.com/snippets/css/a-guide-to-flexbox/](https://dev.toFlexBox%20Guide)
*   MDN 页边空白和关于`margin: auto`-[https://developer.mozilla.org/en-US/docs/Web/CSS/margin](https://dev.toMDN)的章节

现在，我们需要确保:

1.  如果没有填写所有字段，我们的按钮将被禁用
2.  找到一种方法将字段中的值转换成变量

在 React land 中，这将涉及编写大量访问器、函数和 JSX。在这里，我们将以更快的方式将其存档。

Svelte 遵循的方式，由 Vue 铺设，带有自定义，双向绑定。由于苗条的天性，这里甚至更容易。

```
<script>
import { pollState } from "./globals.js";

let name = "";
let amount = 0;
let currency = null;

let changed = false;

$: filled_in = (name !== "") && ( amount > 0 ) && ( currency != null ) && changed;
$: disabled = is_filled_in();

function save() {
    $pollState.poll = {
        name,
        amount,
        currency
    };
}

</script>
<!-- parts of html omitted for brewity -->
<input type="text" id="pollName" required bind:value={name}>
<input type="number" id="pollAmount" required bind:value={amount}> 
<select id="pollCurrency" bind:value={currency} on:change={ev => { changed = !changed; }}></select>
{#if filled_in }
<button class="push-right" type="button" on:click={save} {disabled}>Save</button> 
{/if} 
```

我们缺少一样东西。我们的精选只有一种选择，而且。该选项被禁用，并向用户发送消息。是时候改变这一切了。

```
<select id="pollCurrency" bind:value={currency} on:change={ev => { changed = !changed; }}>
    <option value="" selected disabled>Select Currency</option>
    {#each Object.entries(CURRENCY) as entry }
    <option value={entry[0]}>{entry[1].name}</option>
    {/each} 
<select> 
```

比 React 和普通 JS 函数好吗？很难说，这里我们得到相当简单的`{#each}{/each}`语句。
它给出基本迭代。当然，我们迭代的元素，这里的`CURRENCY`可以是任何 JS 表达式。
如果你需要不同的东西，你可以为它编写函数。

和时间来定义货币，接下来应该在 PollHeader 脚本标记中导入这些货币。

```
/// place it in globals.js and import inside PollHeader
export const CURRENCY = {
    "PLN" : { name: "złoty", format: "{} zł" },
    "USD" : { name: "dollar", format: "$ {}" },
    "EUR" : { name: "euro", format: "{} EUR" }
} 
```

当然，你可以给他们提供财产。

## PollContent 组件

经典 CRUD 应用程序的时间到了。让我从这部分的外观开始。它应该包含几个关键部分:

*   代表我们当前数据的表格
*   控件集
*   带有编辑组件的块

目前最好的想法是将这些部分包装成组件。让我们试试这种方法

在`PollContent.svelte`里面我们会放:

```
<script>
    import { pollState } from "./globals.js";
</script>
<div>
    <h2>{ $pollState.poll.name }</h2>
    <!-- Our table with data  -->
</div>
<div>
    <button type="button">Add Entry</button>
    <button type="button">Update Entry</button>
    <button type="button">Delete Selected Entries</button>
</div>
<div>
    <!-- Our data input part -->
</div> 
```

好吧，为什么我们需要 pollState 商店。我将尝试遵循这个命名约定。如果某个东西在 globals 中以`state`结尾，你应该把它想成 store。

为了使这一切更容易，让我在这里定义几个组件。第一个，接下来是`PollTable.svelte`和`PollInput.svelte`。

```
<script>
    import { pollState } from "./globals.js";
    import PollTable from "./PollTable.svelte";
    import PollInput from "./PollTable.svelte";
</script>
<div>
    <h2>{ $pollState.poll.name }</h2>
    <PollTable />
</div>
<div>
    <button type="button">Add Entry</button>
    <button type="button">Update Entry</button>
    <button type="button">Delete Selected Entries</button>
</div>
<div>
    <PollInput />
</div> 
```

### 轮询表

一般来说，这应该是可以理解的。这里唯一难的部分是三进制的 if 在`<td>`里面。
你应该记住，你可以把任何 JS 表达式放在括号里。
这个`person.amount > 0`表达式检查一个人是否付了钱或者欠了所有的钱，并基于此设置类别。

功能`get_person_by_timestamp`做一件事。迭代我们的数据集，找到时间戳匹配的人。
为什么不是指数？

> 以后的问题:以后如何添加排序？

```
<script>
    import { format_currency, pollState } from "./globals.js";

    function get_person_by_timestamp(ts) {
        for ( let i = 0; i < $pollState.people.length; i++ ) {
            if ( $pollState.people[i].timestamp === ts ) {
                return i;
            }
        }
    }

    function select_checkbox(ts) {
        let index = get_person_by_timestamp(ts);
        $pollState.people[index].selected = !$pollState.people[index].selected;
    }
</script>
<style>
    .paid {
        color: green;
    }

    .owe {
        color: red;
    }
</style>
<table>
    <thead>
        <tr>
            <th>-</th>
            <th>No.</th>
            <th>Person</th>
            <th>Paid</th>
        </tr>
    </thead>
    <tbody>
        {#each $pollState.people as person, index }
        <tr>
            <td><input type="checkbox" on:change={ ev => select_checkbox(person.timestamp) } /></td>
            <td>{index + 1}.</td>
            <td>{person.name}</td>
            <td class = "{ person.amount > 0 ? 'paid' : 'owe' }">{ format_currency(person.amount, person.currency) }</td>
        </tr>
        {/each}
    </tbody>
</table> 
```

我们希望跟踪哪个复选框被选中。最简单的方法可能是在代表 person 的每个对象的 globals.js 中添加 boolean、selected 字段。

现在让我们打开浏览器，使用一些按钮。在第一个面板中填入数值，稍后点击*保存*和*下一步*。
问题是，如果你点击“上一步”，你会看到所有的东西都消失了，或者说，没有任何价值被保留。
为什么？

原因是，我们的`{#if <smth>}`模板将删除并重新添加部分到 DOM 中。
如何解决？

### 返回到我们的表单面板

我们有两个解决方案。

第一个是用旧的 css `display: none;`替换我们的`{#if}`模板。很好，工作正常。
我们的代码现在可能看起来像这样。

```
<style>
.multiform-panel {
    display: block;
}

.multiform-panel.hidden {
    display: none;
}
</style>
<div class="multiform-panel { index !== $controllerState.current ? 'hidden' : '' }">
    <slot></slot>
</div> 
```

但是让我告诉你第二种方法，并向你介绍`onMount`生命周期钩子。
在我们的`PollHeader.svelte`里面我们会做这样的事情:

```
<script>
    /* let me import onMount */
    import { onMount } from "svelte";
    import { CURRENCY, pollState } from "./globals.js";

    onMount(() => {
        name = $pollState.poll.name || "";
        amount = $pollState.poll.amount || 0;
        currency = $pollState.poll.currency || "default";

    })

    let name;
    let amount;
    let currency;
    /* rest goes here */
</script> 
```

生命周期`onMount`在每次组件运行时运行...你猜怎么着。安装到 DOM 中。哪种方式更好？
我觉得，`onMount`好干净一点。

### PollInput

```
<script>
    import { onMount, createEventDispatcher } from "svelte";

    import { CURRENCY, pollState } from "./globals.js";

    export let currency; 

    let dispatch = createEventDispatcher();

    let name;
    let amount;
    let timestamp = null;

    let is_update = false;

    function get_person_to_update() {
        for ( let i = 0; i < $pollState.people.length; i++ ) {
            if ( $pollState.people[i].selected ) {
                return $pollState.people[i];
            }
        }

        return null;
    }   

    onMount(() => {
        let updated = get_person_to_update();

        currency = $pollState.poll.currency;

        if ( updated !== null ) {
            timestamp = updated.timestamp;
            name = updated.name;
            amount = updated.amount;
        } else {
            name = "";
            amount = 0;
            timestamp = null;
        }
    });

    function dispatch_save() {
            dispatch('save', { name, amount, currency, timestamp: timestamp });
    }
</script>
<div>
    <div>
        <label for="name">Name: </label>
        <input id="name" bind:value={name}>
    </div>
    <div>
        <label for="amount">Name: </label>
        <input id="amount" bind:value={amount}>
        <span>{ CURRENCY[currency].name }</span>
    </div>
    <div>
        <button type="button" on:click={ ev => { dispatch_save() } }>Save</button> <!-- [3] -->
    </div>
</div> 
```

好吧，这是怎么回事？您可以看到，到目前为止，我们正在创建自定义事件。这是用于在组件之间传递状态的另一种机制。如果你的组件看起来像输入，自定义事件是一个好主意，如何传递数据。它类似于普通的 DOM 操作，并且以后很容易使用。我们现在的守则。

我们通过以下方式做到这一点:

1.  使用 createNewEvent 创建新的事件调度程序，它将作为返回函数给出。
2.  我们编写小的帮助器函数，重要的细节是，我们不需要编写组件来更新和创建项目。我将在对象中使用时间戳作为标记。事件处理程序可以通过使用类似`ev => ev.detail`的东西来访问这个对象
3.  我把我们的助手，并绑定到按钮上的点击事件。

现在，我们需要填写 PollContent。

### 返回 PollContent

休息至少应该可以理解。我们会用几个道具更新 PollInput 并添加。

首先，我们希望我们的输入框显示点击按钮。
我们需要脚本块中名为`open`的变量，并以这种方式编辑我们的控制按钮；
增加了日志功能，使其在控制台中更加整洁，但可以自由删除。

```
<div class="group horizontal">
    <button type="button" on:click={ev => { console.log(ev.target.innerText); open = true; }}>Add Entry</button>
    <button type="button" on:click={ev => { console.log("%s -- not implemented yet", ev.target.innerText); }}>Remove Selected</button>
    <button type="button" on:click={ev => { console.log(ev.target.innerText); open = true; }}>Update Selected</button>
</div> 

{#if open }
<PollInput on:save={ ev => { create_or_update(ev.detail) }} currency={$pollState.poll.currency}/>
{/if} 
```

我在`PollInput`标签中添加了`on:save`事件处理程序。这是您收听自定义事件的方式。就像以前一样。

这就是我如何实现 create_or_update 函数。

```
function create_or_update(obj) {
        let { name, amount, currency, timestamp } = obj;

        if ( timestamp == null ) {
            let people = $pollState.people;
            people.push(create_person(name, amount));

            $pollState.people = people;  
            open = false;
        } else {
            for ( let i = 0; i < $pollState.people.length; i++ ) {
                if ( $pollState.people[i].timestamp == timestamp ) {
                    $pollState.people[i].name = name;
                    $pollState.people[i].amount = amount;
                    $pollState.people[i].currency = currency;
                    $pollState.people[i].selected = false;
                }
            }
            open = false;
        }
    } 
```

很简单。那些额外的作业是不必要的，但是我喜欢保留它们，因为苗条的作业是特别的。
分配运行整个无功机械。这就是，如果你修改商店或反应的属性，你想分配的每一个变化。

唯一剩下的部分是`Remove Selected`按钮，但我会留给读者作为练习。

同样，如果有什么不清楚的地方，请询问或在 [svelte.dev](https://dev.toSvelte%20Docs%20and%20Api) 上阅读。例子也很酷。

回头见！