# 快速 3:开发人员指南(React)

> [https://dev . to/oieduardorabelo/sverte-3-面向 o-developer-react-impatient-52i5](https://dev.to/oieduardorabelo/svelte-3-guia-para-o-desenvolvedor-react-impaciente-52i5)的指南

## 你需要知道的一切，从快讯 3 开始！

## 目录

1.  什么事这么快？
2.  本指南的适用对象(要求)
3.  你将学习和建设什么
4.  设定专案
5.  快速下潜
6.  快速查找数据
7.  使用“每个”创建列表
8.  帕桑多“道具”
9.  多个“props”和“spreading”
10.  子组件和渲染属性
11.  事件和事件的修改者
12.  一个简短的总结
13.  部署搜索功能
14.  雷蒂维达德
15.  与 React 相比
16.  那么苗条的人会有什么反应呢？
17.  更多功能
18.  最终赞多

# 1。很苗条吗？

rich 是 Rich Harris 创建的用户界面 JavaScript 库。Rich 认为**虚拟礼物**只是负担过重，很快就出现了，现在已经是第三版了。

但你为什么要学得快？反应还是视图还不够？嗯，有一些有趣的地方:

*   快的是**编译器**，而不是试剂或视图等依赖项
*   斯维特似乎需要较少的代码，用于与 React 相同的东西，后者需要 40%以上的 LOC(资料来源:Rich Harris)
*   速递没有**虚拟礼物**，编译了最低限度的“vanilla”JavaScript，似乎比其他库性能更好

我没有做任何基准来证实这些事情，这是你的研究责任。在下一个教程中，我将重点介绍 fast 3 的基本概念。

无论如何，不要跳过这篇文章太快。快的 3 是**真有趣**，尽管它有一些**边**。与此同时，借助本教程可以**快速下潜 3，形成自己的见解**。

祝你好运！

# 2。本指南的适用对象(要求)

如果您对以下内容有基本了解，则可以继续学习本教程:

*   HTML、CSS 和 JavaScript (ES6+)
*   模块 ES6，语法**导入/导出**
*   语法**异步/await**
*   关于组件的概念
*   API 语法 **fetch**

如果你只是刚开始，也许教程对你来说还是太多了。但不要绝望，请查看下面的资源，稍后再回来。

如果您需要更新 ES6 模块，请参阅 JavaScript 文档中的“[**import**T3]和“](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import)[exportT7]”。还有优秀的文章](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/export) [**是深入**](https://ponyfoo.com/articles/es6-modules-in-depth) 的 6 个模块。

e 要了解更多有关 Fetch API 的信息，请查看 [Fetch API](https://davidwalsh.name/fetch) 。

(是的，对于一个新手来说是很多事情。别怪我！页:1。

此外，请确保您的系统上安装了 Node.js 的更新版本。

# 3。你将学习和建设什么

我们不会在本教程中构建“fullstack”应用程序。相反，我将指导您逐步了解 fast 3 的基本知识，并构建用户界面的一些小部分。最后，您应该能够快速开始构建，并了解如何创建组件，如处理事件等。

现在怎么办-我...。我们学得很快！

# 4。设定专案

就像任何现代 JavaScript 项目一样，我们需要播放音乐和跳舞:设置项目！如果要为项目创建 Git 存储库，请继续，然后将该存储库克隆到本地计算机。

克隆后，即可使用“**deg**”创建新的快速项目。别担心，这不是另一个学习工具！ **degit** 只复制 git 存储库，在我们的情况下，我们会将 fast 模型克隆到新文件夹(或我们创建的存储库中)。

总之，如果需要，请创建一个新的 Git 存储库，然后将其复制到本地计算机上。

然后，在新文件夹中创建一个新的快捷项目，其名称为“**degit**”。如果文件夹不空，**度**会通知你，你需要通过强度选项:

```
npx degit sveltejs/template svelte-tutorial --force 
```

然后转到新项目并安装依赖项:

```
cd svelte-tutorial && npm i 
```

现在你可以走了！

# 5。快速下潜

创建文件夹后，我们将查看生成的内容。使用文本编辑器打开项目。您将看到多个文件:

*   **App .快点**—应用的根成分
*   **—一种用于累加的设置，快步使用的包装器**

 **现在打开 **App .快点**看看:

```
<script>
    export let name;
</script>

<style>
    h1 {
        color: purple;
    }
</style>

<h1>Hello {name}!</h1> 
```

这是一个**快速元件**！真的吗？您只需要一个脚本标签、一个样式标签和一点 HTML。 **name** 是 HTML 中使用的变量，**插在键**之间。暂时不要太在意出口申报单。让我们看看接下来会发生什么。

# 6。快速查找数据

为了开始探索速度，我们将立即开始炮击:让我们从一个 API 获取一些数据。

为此，速溶与试剂差别不大，它采用了一种叫做**on count**的方法。是**生命周期功能**。很容易猜测速溶从哪里借用了这个想法:两种**生命周期方法反应**。

现在，我们将创建一个名为 fetch 的新快速组件。在文件夹 **src** 内快速启动。我们的组件从 faz 导入 onMount 并请求 API 查找。**on count**接收回调函数，在此回调中，我们提出请求。数据保存在 onMount 上方名为 **data** 的变量中:

```
<script>
  import { onMount } from "svelte";

  let data = [];

  onMount(async function() {
    const response = await fetch("https://academy.valentinog.com/api/link/");
    const json = await response.json();
    data = json;
  });
</script> 
```

现在打开 **App .快**并导入新创建的组件(除脚本标签外，您可以处理所有内容):

```
<script>
  import Fetch from "./Fetch.svelte";
</script>

<Fetch /> 
```

如您所见，使用组件的语法使人想起 React 的 JSX。由于此组件仅执行 API 调用，但尚未显示任何内容，因此我们将向混合中添加更多内容。

# 7。使用“每个”创建列表

在 React 中，我们习惯了创建元素列表的映射功能。很快就有一个叫" each "的区块。我们将使用它创建链接列表。API 返回对象数组，每个对象都有一个标题和一个 URL。我们现在加一个“每”块:

```
<script>
  import { onMount } from "svelte";

  let data = [];

  onMount(async function() {
    const response = await fetch("https://academy.valentinog.com/api/link/");
    const json = await response.json();
    data = json;
  });
</script>

{#each data as link}
// faz algo //
{/each} 
```

观察“每一个”t0]是如何产生可变数据，我从其中提取每一个元素作为**【link】**。以区块为单位，现在是产生元素清单的时候了，请确认在区块「每」周围放置一个元素**【ul】**:

```
<script>
  import { onMount } from "svelte";

  let data = [];

  onMount(async function() {
    const response = await fetch("https://academy.valentinog.com/api/link/");
    const json = await response.json();
    data = json;
  });
</script>

<ul>
  {#each data as link}
    <li>
      <a href={link.url}>{link.title}</a>
    </li>
  {/each}
</ul> 
```

现在，在终端上，进入您的项目文件夹并运行:

```
npm run dev 
```

访问**[【http://localhost:5000/](http://localhost:5000/)**，您将看到链接列表:

[![](img/e59a0f28db00048ca72cf7bf79ac904a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--X38Vo8Rw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.valentinog.com/blog/wp-content/uploads/2019/06/svelte-list-of-elements-each.png)

干得好！您已学习如何快速生成项目列表。但现在让我们使**我们的成分更加可重用**。

# 8。通过" props "

**在用户界面中重用组件的能力**是现代 JavaScript 库的“主要原因”。例如，在 React 中，有“**propes”**的概念，自定义属性(甚至功能或其他组件)我们可以传递给我们的组件，使它们更加灵活。

目前，“T0”提取。快点“”不那么重用，URL 是唯一的。但别担心，急流的成分也可以从外面得到 **props** 。让我们先把 url 做为变数(我只展示元件的相关部分):

```
<script>
  import { onMount } from "svelte";

  let url = "https://academy.valentinog.com/api/link/";
  let data = [];

  onMount(async function() {
    const response = await fetch(url);
    const json = await response.json();
    data = json;
  });
</script> 
```

好吧，现在有一个技巧使网址成为属性:只需将变量前缀为 **export** 。

```
<script>
  import { onMount } from "svelte";

  // exporta a variável para transforma-la em uma prop
  export let url = "https://academy.valentinog.com/api/link/";
  let data = [];

  onMount(async function() {
    const response = await fetch(url);
    const json = await response.json();
    data = json;
  });
</script> 
```

现在，打开“**app . fast〖t1〗并通过传递 url 属性:
更新 Fetch 组件**

```
<script>
  import Fetch from "./Fetch.svelte";
</script>

<Fetch url="https://jsonplaceholder.typicode.com/todos" /> 
```

现在，您的组件调用新端点，而不是缺省 URL。另一个有趣的是标记为 props 的变量可以具有默认值。在我们的示例中，“[https://academy . Valentino log . com/API/link/](https://academy.valentinog.com/api/link/)**是默认值**，在未传递属性时用作替换。

现在让我们来看看当我们需要另一个道具时会发生什么。

# 9。多个“props”和“spreading”

快速元件当然可以有多个 props。我们将在我们的组件中添加另一个名为的标题的标题:

```
<script>
  import { onMount } from "svelte";

  export let url = "https://academy.valentinog.com/api/link/";
  export let title = "A list of links";
  let data = [];

  onMount(async function() {
    const response = await fetch(url);
    const json = await response.json();
    data = json;
  });
</script>

<h1>{title}</h1>
<ul>
  {#each data as link}
    <li>
      <a href={link.url}>{link.title}</a>
    </li>
  {/each}
</ul> 
```

又从 app .快点我们换了一个新的属性:

```
<script>
  import Fetch from "./Fetch.svelte";
</script>

<Fetch
  url="https://jsonplaceholder.typicode.com/todos"
  title="A list of todos" /> 
```

当 props 开始大量增长时，您可能会发现上述方法不切实际。幸运的是，有一种方法一次传播所有的命题。将 props 宣告为物件，并将它们(t1]散布于元件:

```
<script>
  import Fetch from "./Fetch.svelte";
  const props = {
    url: "https://jsonplaceholder.typicode.com/todos",
    title: "A list of todos"
  };
</script>

<Fetch {...props} /> 
```

很酷吧？但我还是不满意。我想使 Fetch 组件**更易于重用**。怎么做？

# 10。子组件和渲染属性

“Fetch”对于组件来说不是一个坏名称，但如果我查看它，它将返回 HTML 列表。有没有办法把这份名单传到外面，**作为试剂**中的属性子？是的，有可能！不要太快，我们称子元件为**插槽**。

作为第一步，我将去掉提取的全部标记。快点，代之以**插槽**。我们将移除属性**标题**也:

```
<script>
  import { onMount } from "svelte";

  export let url = "https://academy.valentinog.com/api/link/";
  let data = [];

  onMount(async function() {
    const response = await fetch(url);
    const json = await response.json();
    data = json;
  });
</script>

<slot /> 
```

接下来，我可以从父元素中将子元素传递到提取，这种情况发生在 **App .快点** :

```
<script>
  import Fetch from "./Fetch.svelte";
  const props = {
    url: "https://jsonplaceholder.typicode.com/todos"
  };
</script>

<Fetch {...props}>
  <h1>A list of todos</h1>
  <ul>
    <li>now what?</li>
  </ul>
</Fetch> 
```

但现在我们有麻烦了。我需要提取中包含的数据。快点，因为我不想手动创建列表。

在 React 中，您会使用**ad hoc、render props 或[hook](https://www.valentinog.com/blog/hooks)T3]。换句话说，我想渲染子组件，但子组件必须从父组件中获取数据。**

在“快速”中，通过将值传递回父零部件可以获得相同的结果。首先**将数据作为属性传递到其插槽** :

```
<script>
  import { onMount } from "svelte";

  export let url = "https://academy.valentinog.com/api/link/";
  export let title = "A list of links";
  let data = [];

  onMount(async function() {
    const response = await fetch(url);
    const json = await response.json();
    data = json;
  });
</script>

<!-- {data} é um atalho para data={data} -->
<slot {data} /> 
```

并且在外面，可以用**表示法访问**日期【let:data】，这里缩短为**【let:data】**:

```
<script>
  import Fetch from "./Fetch.svelte";
  const props = {
    url: "https://jsonplaceholder.typicode.com/todos"
  };
</script>

<!-- "let:data" é como encaminhar os dados internos de um componente um nível para cima. -->
<Fetch {...props} let:data>
  <h1>A list of todos</h1>
  <ul>
    {#each data as link}
      <li>{link.title}</li>
    {/each}
  </ul>
</Fetch> 
```

现在我可以用**提取**成分中的日期，它们将适用于每块。这就像将组件的内部数据向上传递一级。

听起来是个不错的办法，虽然一开始可能会反直觉。你觉得呢？在下一节中，我们将很快看到对事件的处理。

# 11。事件和事件的修改者

我们将构建一个表单组件，以说明 fast 如何处理事件。创建一个名为**form . move**的新文件。目前，它将包含一个搜索条目和一个提交按钮:

```
<script>

</ script>

<form>
  <label for = "search"> Pesquisa: </ label>
  <input type = "search" id = "search" obrigatório />
  <button type = "submit"> Pesquisar </ button>
</ form> 
```

(作为练习，您可以提取自己组件中的所有元素。)。

然后将新元件加入 **App .快点** :

```
<script>
  import Form from "./Form.svelte";
</script>

<Form /> 
```

现在，应用程序必须在浏览器中呈现其窗体。此时，如果您尝试提交表单，则默认行为是:浏览器将刷新页面。

为了控制 JavaScript“vanilla”中的表单，我将为 submit 事件注册一个事件侦听器。因此，在处理程序中，我会避免使用**event . prevent default()**:

```
// vanilla JS example
var form = document.getElementsByTagName('form')[0]

form.addEventListener('submit', function(event){
    event.preventDefault();
}); 
```

快速组件内的情况稍有不同:**事件处理程序以【on】**注册，后跟处理程序的事件/功能名称:

```
<script>
  function handleSubmit(event) {
    // faz algo
  }
</script>

<form on:submit={handleSubmit}>
  <label for="search">Search:</label>
  <input type="search" id="search" required />
  <button type="submit">Search</button>
</form> 
```

另外，在 fast 中**有事件修饰符**。最重要的是:

*   **preventDefault**
*   **停止传播**
*   **一次**

对于**中断标准效果**在我们的表格中，我们可以使用修饰符**【防止故障】**，在事件名称之后:

```
<script>
  function handleSubmit(event) {
    // faz algo
  }
</script>

<form on:submit|preventDefault={handleSubmit}>
  <label for="search">Search:</label>
  <input type="search" id="search" required />
  <button type="submit">Search</button>
</form> 
```

也可以通过**【handle bmit】**作为支撑，使零部件更加灵活。这里有一个例子，有一个标准属性:

```
<script>
  export let handleSubmit = function(event) {
    // faz algo
  };
</script>

<form on:submit|preventDefault={handleSubmit}>
  <label for="search">Search:</label>
  <input type="search" id="search" required />
  <button type="submit">Search</button>
</form> 
```

就是这样。现在让我们把这个简单的应用程序向前推进一步:**我想过滤一下链接列表**。表格已经生效，但我们需要连接**提取。快**与**表单。快**。让我们做到这一点！

# 12。一个简短的总结

让我们总结一下我们迄今为止所做的工作。我们有两个成分，**提取。快点** :

```
<script>
  import { onMount } from "svelte";

  export let url = "https://academy.valentinog.com/api/link/";

  let data = [];

  onMount(async function() {
    const response = await fetch(url);
    const json = await response.json();
    data = json;
  });
</script>

<slot {data} /> 
```

婀娜多姿 :

```
<script>
  export let handleSubmit = function(event) {
    // valor padrão da prop
  };
</script>

<form on:submit|preventDefault={handleSubmit}>
  <label for="search">Search:</label>
  <input type="search" id="search" required />
  <button type="submit">Search</button>
</form> 
```

接下来是 **App .快点**，它是根成分。为了方便起见，我们将呈现表格并在应用程序内搜索:

```
<script>
  import Fetch from "./Fetch.svelte";
  import Form from "./Form.svelte";
</script>

<Form />
<Fetch let:data>
  <h1>A list of links</h1>
  <ul>
    {#each data as link}
      <li>
        <a href={link.url}>{link.title}</a>
      </li>
    {/each}
  </ul>
</Fetch> 
```

**提取。快点**从 API 中提取数据，将数据发送到顶层。因此，通过将每个数据块用作插槽，我可以将数据传递给您的孩子。

现在，我希望用户根据在表单中输入的搜索条件过滤数据。似乎**形式**和**提取**需要沟通。让我们看看如何实现这一点。

# 13。实现搜索功能

我看到此功能的方式是，我们需要一个搜索项来筛选数据阵列。搜索项可以是传递给 **Fetch 的 prop。从父组件中快速**。打开**提取。快点**并添加新属性 **searchTerm** :

```
<script>
  import { onMount } from "svelte";

  export let url = "https://academy.valentinog.com/api/link/";

  // nova prop
  export let searchTerm = undefined;

  let data = [];

  onMount(async function() {
    const response = await fetch(url);
    const json = await response.json();
    data = json;
  });
</script>

<slot {data} /> 
```

(**search term**éatribuído**undefined**para evitar que Svelte alert no console“Fetch foi criado SEM a prop search term esperada”)。

接下来，我们需要一个新变量来维护 json 中的响应，因为我们将根据 searchTerm 过滤此响应。添加一个名为 **jsonResponse** 的新变量，并使用 **jsonResponse** 存储 API 响应:

```
<script>
  import { onMount } from "svelte";

  export let url = "https://academy.valentinog.com/api/link/";

  // nova prop
  export let searchTerm;

  // nova variável
  let jsonResponse = [];

  let data = [];

  onMount(async function() {
    const response = await fetch(url);
    const json = await response.json();

    // salva a resposta na nova variável
    jsonResponse = json;
  });
</script>

<slot {data} /> 
```

此时，日期将有:

*   如果未提供 searchTerm，则返回原始 jsonResponse
*   searchTerm 不为空时筛选的数组

要筛选数组元素，我们可以使用属性**【title】**基于 [RegExp](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RegExp) 的匹配。(API 会传回物件阵列。每个对象都有**标题**和 **url** 。第一个实现可能是:

```
const regex = new RegExp(searchTerm, "gi");

const data = searchTerm
    ? jsonResponse.filter(element => element.title.match(regex))
    : jsonResponse; 
```

Faz sentido！完整组件的版本:

```
<script>
  import { onMount } from "svelte";

  export let url = "https://academy.valentinog.com/api/link/";

  // nova prop
  export let searchTerm = undefined;

  // nova variável
  let jsonResponse = [];

  const regex = new RegExp(searchTerm, "gi");

  const data = searchTerm
    ? jsonResponse.filter(element => element.title.match(regex))
    : jsonResponse;

  onMount(async function() {
    const response = await fetch(url);
    const json = await response.json();

    // salva a resposta na nova variável
    jsonResponse = json;
  });
</script>

<slot {data} /> 
```

此时，我们需要对 app 进行一些调整。快点。searchTerm 必须是由父组件发送的动态 prop。然后，当用户提交表单时，我们截获输入值。打开 **App .快点**并传递 searchTerm 作为提取属性:

```
<script>
  import Fetch from "./Fetch.svelte";
  import Form from "./Form.svelte";

  let searchTerm;
</script>

<Form />
<Fetch {searchTerm} let:data>
  <h1>A list of links</h1>
  <ul>
    {#each data as link}
      <li>
        <a href={link.url}>{link.title}</a>
      </li>
    {/each}
  </ul>
</Fetch> 
```

接下来，我们创建并传递 handlesubmit 作为窗体的属性，并在 App 中保存用户输入的搜索术语 searchTerm:

```
<script>
  import Fetch from "./Fetch.svelte";
  import Form from "./Form.svelte";

  let searchTerm;

  function handleSubmit() {
    const { value } = this.elements.search;
    searchTerm = value;
  }
</script>

<Form {handleSubmit} />
<Fetch {searchTerm} let:data>
  <h1>A list of links</h1>
  <ul>
    {#each data as link}
      <li>
        <a href={link.url}>{link.title}</a>
      </li>
    {/each}
  </ul>
</Fetch> 
```

快好了。保存所有文件并运行开发服务器。你会看到的-我...。一页空白！

[![](img/4e1973a44f68042ca66ae193ca2a25d2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rzqEGEmL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.valentinog.com/blog/wp-content/uploads/2019/06/svelte-3-tutorial-reactivity.png)

这是怎么回事？抓紧，转到下一节！

# 14。反应能力

fast 处理**计算值**的方式起初可能看起来不直观。我们的问题在于**提取。快点**，它来自以下行:

```
const regex = new RegExp(searchTerm, "gi");

const data = searchTerm
    ? jsonResponse.filter(element => element.title.match(regex))
    : jsonResponse; 
```

仔细想想，我们有两个计算值。RegExp 依赖 searchTerm，并且我们希望在更改最后一个时重新计算第一个。

所以我们有变量 **data** :每当 searchTerm 和 RegExp 更改时，它也必须重新计算。就像电子表格:值可能取决于其他值。

fast 受“反应性编程”的启发，对所谓的计算值使用了一种奇怪的语法。这些数值在速递 3 中称为**【反应性语句】**。参见上面的代码应如何调整:

```
$: regex = new RegExp(searchTerm, "gi");

$: data = searchTerm
    ? jsonResponse.filter(element => element.title.match(regex))
    : jsonResponse; 
```

**$:** 不是外星人语言。它只是 JavaScript，并被称为[标签为](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/label)的指令。

这里是提取。快点再次完成:

```
<script>
  import { onMount } from "svelte";

  export let url = "https://academy.valentinog.com/api/link/";
  export let searchTerm = undefined;
  let jsonResponse = [];

  $: regex = new RegExp(searchTerm, "gi");

  $: data = searchTerm
    ? jsonResponse.filter(element => element.title.match(regex))
    : jsonResponse;

  onMount(async function() {
    const response = await fetch(url);
    const json = await response.json();
    jsonResponse = json;
  });
</script>

<slot {data} /> 
```

Agora，o recurso de pesquisa funcionarácomo esperado:

[![](img/b90e971ab00591d20bd3ae06f010789c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--j8ngla5V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.valentinog.com/blog/wp-content/uploads/2019/06/svelte-3-search-form.png)

(在 API 级别过滤链接比每次都查找所有链接更好。)。

现在，请查看下一节，如果您想了解如何使用 React 实现相同的“应用程序”。

# 15。Comparando com 反应

相同的应用程序将如何反应？这里是 app . js，相当于 **App .快点** :

```
import React, { useState } from "react";
import Fetch from "./Fetch";
import Form from "./Form";

function App() {
  const [searchTerm, setSearchTerm] = useState("");

  const fetchProps = {
    url: "https://academy.valentinog.com/api/link/",
    searchTerm
  };

  function handleSubmit(event) {
    event.preventDefault();
    const { value } = event.target.elements.search;
    setSearchTerm(value);
  }

  return (
    <>
      <Form handleSubmit={handleSubmit} />
      <Fetch
        {...fetchProps}
        render={links => {
          return (
            <>
              <h1>A list of links</h1>
              <ul>
                {links.map(link => (
                  <li key={link.url}>
                    <a href={link.url}>{link.title}</a>
                  </li>
                ))}
              </ul>
            </>
          );
        }}
      />
    </>
  );
}

export default App; 
```

在此，我将 Fetch 组件与渲染属性一起使用。我可以用钩子，但我想让你看看同样的概念如何适用于快速反应和反应。

换句话说:

*   要从 React 中的子组件访问父组件的状态，可以使用 render props(或用于数据搜索共享的自定义挂接)
*   若要存取快速插槽中父元件的状态，您可以从父项向上传输资料

如果将 **App.js** 与快速版本([单击此处](https://github.com/valentinogagliardi/svelte-tutorial/blob/master/src/App.svelte)进行比较，您会发现典型的快速组件比试剂中的等效组件更不详细。

简单地解释一下，在 fato 中没有必要显式调用 setSomeState 或类似的函数。快速“反应”仅通过为变量赋值来实现。

接下来， **Form.js** 是 **Form 的反应镜。快点** :

```
import React from "react";

function Form(props) {
  return (
    <form onSubmit={props.handleSubmit}>
      <label htmlFor="search">Search:</label>
      <input type="search" id="search" required={true} />
      <button type="submit">Search</button>
    </form>
  );
}

export default Form; 
```

没什么大不了的，只有一个功能得到了一些建议。

最后还有**提取。js** 镜像 o **提取。快点** :

```
import { useState, useEffect } from "react";

function Fetch(props) {
  const { url, searchTerm } = props;
  const [links, setLinks] = useState([]);
  const regex = new RegExp(searchTerm, "gi");
  const data = searchTerm
    ? links.filter(link => link.title.match(regex))
    : links;

  useEffect(() => {
    fetch(url)
      .then(response => response.json())
      .then(json => setLinks(json));
  }, [url]);

  return props.render(data);
}

Fetch.defaultProps = {
  url: "https://academy.valentinog.com/api/link/"
};

export default Fetch; 
```

以上组件使用挂接和渲染属性:同样，这是不必要的，因为您可以将所有内容提取到自定义挂接中。这里是
提取。快点 :

```
<script>
  import { onMount } from "svelte";

  export let url = "fillThis";
  export let searchTerm = undefined;
  let jsonResponse = [];

  $: regex = new RegExp(searchTerm, "gi");

  $: data = searchTerm
    ? jsonResponse.filter(element => element.title.match(regex))
    : jsonResponse;

  onMount(async function() {
    const response = await fetch(url);
    const json = await response.json();
    jsonResponse = json;
  });
</script>

<slot {data} /> 
```

对我来说，他们很相似！但是，这些示例并不接近于大型应用程序，而且它们的里程可能会有所不同。

# 16。那么苗条的人会有什么反应呢？

他们问我对反应堆和 vista 的反应有多快。我不能凭视力说话，因为我没有太多的经验，但我能看出他借了一些东西怎么快。

说到 React，快点给我好好想想，它看起来比你的同类更直观。一眨眼之间，3 号快车似乎只是一种做事的方式，也许比反应堆聪明。但它的基础是坚实的。

真正有趣的是，很快就没有虚拟天赋了，除了 React 和 virtual。也就是说，在库和“T0”文档对象模型之间没有抽象:速写 3 编译尽可能少的“vanilla”JavaScript。如果您在受限环境中运行应用程序，此功能非常有用。

简而言之，速递是一个相当有趣的图书馆，但至少我会给更多的时间，直到文档、生态系统和工具成熟。

# 17。更多功能

为了进一步了解速动，我不能充分推荐官方文件[和](https://svelte.dev/examples)实例。

[此处提供本教程的源代码](https://github.com/valentinogagliardi/svelte-tutorial)。

我还建议您观看这个速递创作者的演示:

[https://www.youtube.com/watch?v=AdNJ3fydeao](https://www.youtube.com/watch?v=AdNJ3fydeao)

# 18。最终赞多

从这里去哪里？如果你愿意的话，还有很多关于短跑 3 的知识。现在有很多新功能可供使用:

*   Estilos com escopo ( *作用域样式*)
*   数据绑定 de duas 过孔(*双向数据绑定*)
*   状态管理〔t0〕状态管理〔t1〕
*   内置动画(“T0”构建于动画

在告别之前，我想说几句结束语:

JavaScript 很残忍。图书馆来来去去，总有新东西要学。多年来，我学会了不要太拘泥于任何特定的 JavaScript 库，但老实说，我真的很喜欢 React 和 Redux。

React 给群众带来了“构件”，另一方面，图书馆本身需要高度专业化才能掌握。相比之下 isso 对初学者来说更友好，但遗憾的是却没有被看作是反应(这意味着什么)。

fast 3 具有两个世界中最好的一个:fast 组件与 vista 中的组件相似，某些 React 概念也适用。

快动作比反应战更直观尤其是当我们想到胡克斯时代反应战的初学者时。但是，试剂并没有这么快就走，但是我期待着看它快去哪里。

而对于你，我的朋友，总是有我以前的建议:永远学习 web 平台，你将能够掌握接下来的任何 JavaScript 库。

感谢您阅读并登录此博客！

# [t1【学分】](#cr%C3%A9ditos-%EF%B8%8F)

*   为缺乏耐心的开发者准备的简明教程，原创者 escrito por[Valentino galliardi](https://twitter.com/gagliardi_vale)**