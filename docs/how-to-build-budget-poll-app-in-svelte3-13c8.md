# 如何在 Svelte3 中构建预算投票应用程序

> 原文：<https://dev.to/corvusetiam/how-to-build-budget-poll-app-in-svelte3-13c8>

# 简介

我想从解释我自己开始，为什么我使用另一个几乎不为人知的框架，其中大多数人只是停留在使用 Angular，React，Vue，也许还有一两个。好吧，最好的解释将是关于小的学习曲线(可与原始 HTML5 相比)和老派技术、速度、小尺寸和少量样板文件的组合。

在这里，我想为你预设，如何建立小的，可用的，钱投票应用程序。这是不多，它不会赢得任何网站选美。

如果你很好地掌握了 HTML5 和 Javascript，这将是一个好主意。

## 金钱民调？

让我给你看一些简单的例子。你要和你的朋友一起去吃披萨，但是有一个问题。城里最好的比萨饼店不接受信用卡支付，自动取款机离这里很远，而且不是每个人手头都有足够的现金来支付账单。我们该怎么办？这很简单。我们拿着收据，把它平均分给每个人。每个人都尽可能多的付钱，然后我们会互相偿还账单，或者只是用我们的手机互相寄钱。容易吗？是的。

正常人是怎么做到的？

1.  带计算器
2.  按人数划分食谱
3.  每个人的平均价格-投在投票中的钱= >其他人欠这个人/女孩多少钱，或者他们欠了多少钱。

dev 是怎么做到的？

1.  打开 Excel
2.  转到:正常人的算法

webdev 是如何做到的？

1.  为它建了网站。
2.  独自坐在餐厅里，因为几个小时前，你的其他朋友达成了协议，然后高高兴兴地回家。餐馆老板现在看你的眼神怪怪的。

## 为什么身材苗条又没有反应？

免责声明:我打算在 React 中做同样的事情。

这就是我们应用程序中基本数据的经典输入组件的外观:

```
class BasicInfo extends React.Component {
    render() {
         return (
      <fieldset>
        <legend>Poll</legend>
        <p>Provide name and amount of money for your poll</p>
        <p>
          <label htmlFor="pollName">Name: </label>
          <input
            type="text"
            id="pollName"
            name="pollName"
            onChange={ev => this.props.onChangeName(ev.target.value) }
          />
        </p>
        <p>
          <label htmlFor="pollAmount">Amount: </label>
          <input
            type="number"
            id="pollAmount"
            name="pollAmount"
            onChange={ev =>
              this.props.onChangeAmount(parseInt(ev.target.value, 10))
            }
          />
        </p>
        <button type="button" className="active"
          onClick={() => {
            this.props.onSave();
          }}
        >
          Save
        </button>
      </fieldset>
    );
  }
}
} 
```

呸，这还不是全部。说真的，由于 react 的额外类型注释，我的代码几乎花了两倍的时间。它仍然缺少构造函数、默认属性和状态的大量代码。

现在为了苗条。

```
<script>

    const CURRENCY = {
        "PLN" : { name: "złoty" },
        "USD" : { name: "dollar" }
    }

    let name = "";
    let amount = 0;
    let currency;

    function save() {
        /// here goes save function. Cut for brewity
    }

    $: is_filled = ( name !== "" ) && ( amount > 0 ) && (currency !== undefined);
</script>
<fieldset>
    <legend>Poll Start</legend>
    <p>Please provide basic data about poll</p>
    <label for="pollName">Poll Name: </label>
    <input type="text" id="pollName" bind:value={name} required>
    <label for="pollAmount">Poll Name: </label>
    <input type="number" id="pollAmount" bind:value={amount} required>
    <select bind:value={currency}>
        <option value="default" disabled selected>Select Currency</option>
        {#each Object.entries(CURRENCY) as entry }
        <option value={entry[0]}>{ entry[1].name }</option>
        {/each} 
    </select>
    {#if is_filled }
    <button type="button" on:click={save}>Save</button>
    {/if}
</fieldset> 
```

如果你没有完全理解，不要担心。这里最重要的部分是，原始项目中细长的代码用了大约 32 行代码来实现这一切。

## 我撒谎了...抱歉。

说真的，苗条不是框架。如果你访问他们的网站 [SvelteDev](http://svelte.dev) ，你只会发现一些奇怪的文字，即:**控制论增强的网络应用**。
这听起来像是对一群经理说的时髦话，向他们推销又一个无用的项目。别担心，没那么糟。他们可能想说的是:苗条不是一个框架。一点也不。

你应该考虑像 Typescript、Vue 单文件模板或 Babel 这样的东西。它是为你生成样板文件的工具。各种各样的编译器，不要害怕。里面没有多少龙...

# 开始时间

首先，我们应该设置我们的环境。现在你有两条可能的路。

1.  使用 [codesandbox.io](//codesandbox.io) 并使用 Github 凭证登录。然后点击“创建沙箱”按钮，从“客户端沙箱”选项卡中选择*瘦沙箱*。

2.  用编辑器在本地设置 svelte。我使用 VSCode，但是像 Notepad++这样的软件就足够了。你只需要支持 HTML5 的编辑器。

我会告诉你第二条路。

## 安装

您的系统上应该有工作节点和 npm。此外，我们将使用 git 进行良好的实践。如果没有，拿一把[https://nodejs.org/en/download/](https://dev.toinstaller%20now%20and%20install%20it)。
其次打开 shell 或命令行，输入

```
npx degit sveltejs/template budget-poll 
```

Npx 是包装 npm 的工具。它下载并安装作为第二个参数提供工具，并使用提供的其余参数运行该工具。
这里我们用`degit`。Svelte 的作者 Rich Harris 编写的工具，通过从 git repo 中克隆模板并将所有内容放在名为`budget-poll`的文件夹中来设置项目。
他喜欢重新发明轮子，但他用它做了一些奇妙的事情。
现在标准的 git 设置和项目安装:

```
cd budget-poll
git init
git add *
git commit -am "Initial setup of budget app"
npm install 
```

过了一会儿，下载了大量的互联网，我们有了工作环境。现在只需输入`npm run dev`并打开浏览器，一个地址就会出现在你的终端上。对我来说，是`localhost:5000`。

## 一般组件布局

首先，我们应该考虑如何布局我们的组件。我们需要的东西很少。我很想把预算分成几个部分，并在这些页面之间添加一些方式。
这意味着我们需要`MultiPanelForm`和`FormPanel`组件。让我把它反过来写`App.svelte`。

```
<script>
    // App.svelte content
</script>
<MultiPanelForm>
    <FormPanel>
        <!-- First panel -->
    </FormPanel>
    <FormPanel>
        <!-- Second panel -->
    </FormPanel>
    <FormPanel>
        <!-- Third panel -->
    </FormPanel>
</MultiPanelForm> 
```

好吧，看起来很简单。如果你曾经看过如何使用 JSX，它是相似的。或者坦率地说，就像你用 HTML5 写一样。

那些大写的非标准标签是我们的组件。为了使用它们，我们需要从其他地方进口。
为此，添加带有`App.svelte`脚本标签的 ES6 风格导入。记住使用相对路径并在文件名中添加扩展名。苗条的人不会快乐，因为他们还不存在。别担心，大男孩，我们马上就做。同时从`main.js`中移除属性`props: { ... }`。你不打算用它。

```
import MultiPanelForm from "./MultiPanelForm.svelte";
import FormPanel from "./FormPanel.svelte"; 
```

现在，你认为这很容易。现在会有 JSX 的疯狂，js 的风格什么的，就像 React land 一样。好吧，让我们来看看。

## 多格式面板

创建名为`MultiFormPanel.svelte`的文件。这是第一个可重用组件。它包含标签中包含的自由部分。将这些添加到文件中。

```
<script>
    /// mostly JS and some svelte-specific extensions
</script>
<style>
    /* css styling for your component */
</style>
<form>
    <!-- html part of component and svelte templating -->
</form> 
```

什么是特定于 svelte 的扩展和模板？好吧，给我点时间。
创建另一个名为`FormPanel.svelte`的文件，并将该布局复制到新创建的文件中。只需用 div 替换`form`标签。

当你打开浏览器时，svelte 应该能正确显示所有内容。我的意思是，除了空洞的形式，你什么也看不到，但它是有效的。现在，是时候处理组件中的定制组件了。我们必须提供一个目标，一个放置这些组件的地方。
一个槽！怎么做呢？

是时候编辑`MultiFormPanel.svelte`并添加一些 html 了。

```
<form>
    <div class="container">
        <slot></slot>
    </div>
    <div class="controller"></div>
</form> 
```

好吧，我向前跳了一点。如果你知道任何反应 JSX 比，你会注意到，我们没有使用任何`className`在这里，或任何奇怪的命名标签。只是老派 html 加自定义(或者不那么自定义)，槽标签。
Slot 是该组件的子组件将自动进入的地方。为了更好地理解这一点，打开 [svelte.dev](//svelte.dev) 页面，阅读非常棒的互动教程。

我们还将编辑:`FormPanel`并输入这个 html。

```
<div class="multiform-panel">
    <slot></slot>
</div> 
```

好的。有了一些通用的 HTML，现在我们需要添加功能。第一个按钮除此之外，他们应该只出现在
当某些标准得到满足。
但首先我们需要一种方法来存储我们到底在哪一页。
在同一个`./src`目录下创建`globals.js`文件并在那里键入这些文本。

```
/* writable is a way to keep global state in svelte, just like context api, 
just simpler and easier to split in smaller parts
*/
import { writable } from "svelte/store";

/* here we define and export controllerState store. We can now subscribe in other files to this store and update its content */
export const controllerState = writable({
    current: 0
}) 
```

现在是时候来点控制论的增强版 js 了...或者仅仅是普通 js 加上一些扩展以减少样板文件。

打开`MultiFormPanel.svelte`，先添加两个控制按钮。当满足某些标准时，这些按钮应该会出现。
为了存档，我们将使用类似于`Mustache`模板中已知的模板。

```
<div class="controller">
    {#if prev_active}
    <button type="button">Previous</button>
    {/if}
    {#if next_active}
    <button type="button">Next</button>
    {/if}
</div> 
```

`prev_active`和`next_active`都是布尔变量。现在让我们来定义它们。

```
<script>
    import { controllerState } from "./globals.js";

    export let last_page;

    let prev_active = $controllerState.current > 0;
    let next_active = $controllerState.current < last_page;
</script> 
```

我们这里没什么新东西。第一个`export let last_page`。这就是 svelte 实现属性的方式。
只要`export let <prop_name> = <default_value>`你就可以出发了。

现在你可以编辑`App.svelte`到`<MultiFormPanel last_page={2}>`中的`<MultiFormPanel>`来传递属性。

那些美元是干什么用？我没有进口那个，是吗？

是的...从某种角度来看...

那些都是语法上的糖。

```
let prev_active;

controllerState.subscribe(val => {
    prev_active = (val.current > 0);
}) 
```

多亏了它们，现在你可以像普通变量一样访问 store。

问题是...没用。我们需要更多的代码。
首先让我只让`index`道具等于`$controllerState.current`的面板可见。

在`FormPanel.svelte` :

```
<script>
    import { controllerState } from "./globals.js";

    export let index;
</script>
{#if index == $controllerState.current }
<div class="multiform-panel">
    <slot></slot>
</div>
{/if} 
```

并在`App.svelte`内添加`index`道具。

应该是这样的。

```
<MultiPanelForm last_page={2}>
    <FormPanel index={0}>
        <!-- First panel -->
    </FormPanel>
    <FormPanel index={1}>
        <!-- Second panel -->
    </FormPanel>
    <FormPanel index={2}>
        <!-- Third panel -->
    </FormPanel>
</MultiPanelForm> 
```

为了使切换工作打开`MultiFormController.svelte`并在`<script>`块中添加一些东西。

```
function next_panel() {
    $controllerState.current = $controllerState.current + 1;
}

function prev_panel() {
    $controllerState.current = $controllerState.current + 1;
} 
```

并将这些事件添加到相应的按钮中。就像这样:

```
<button type="button" on:click={prev_panel}>Previous</button> 
```

类似于`Next`按钮。有些不对劲。按钮没有像它们应该的那样改变。如何处理更新变量“prev_active”和“next_active”？

它们被分配了一次，我们没有改变它们。它们会自动重新计算吗？

没有。没有什么是那么容易的！要做到这一点，我们需要付出一些努力。
在`MultiFormPanel.svelte`里面，在`<script>`块里面。是时候施展一些黑魔法了。

准备好了。

## 设定！走吧。

```
 let prev_active = $controllerState.current > 0;
    let next_active = $controllerState.current < last_page; 
```

你有这个。现在，为了让一切都有反应，我们需要改变一些事情。怎么会？

来了，苗条中最好的概念之一。
只要把`let`换成`$:`就可以了。

```
$: prev_active = $controllerState.current > 0; 
```

## 等等！？什么？怎么会？

记住，我告诉过你 Svelte 是编译器而不是框架。这给了他们改变语言本身的能力。
这通常不会有什么问题。这里我们得到了几乎免费的，成熟的反应变量。
随时`$controllerState.current`都会变，会更新`prev_active`和`next_active`。

## 最终的东西

```
git add src\*
git commit 
```

并编写一些有用的 git commit。现在按下`<ESC>`并输入`:wq!`然后按下`<Enter>`。如果你需要更多的帮助，请阅读使用`Vim`进行编辑，或者将你默认的 vim 编辑器改为`nano`或其他...

感谢阅读。我们会回来买更多的！

## 一些统计数据。

反应:

*   花了我:120 行代码存档几乎一样。
*   在我的坏笔记本电脑上编译:70 秒左右，当使用包裹
*   包括融合孩子，用道具增强他们。如果没有打字稿和包裹，我会很生气

苗条:

*   组件中的 40 行代码。
*   带有内置模块支持的简易 CSS
*   生成的代码要小得多。
*   易于使用，易于阅读的版本。
*   也可以使用苗条的打字稿！