# 使用微格式离线

> 原文：<https://dev.to/adactio/going-offline-with-microformats-47c2>

*这篇文章最初发表于[我自己的网站](https://adactio.com/journal/15844)。*

对于我网站上的离线页面，我混合使用了缓存 API 和`localStorage` API。我的服务工作者脚本使用缓存 API 来存储页面的副本，以便离线检索。但是我使用了`localStorage` API 来存储关于页面的元数据——标题、描述等等。然后，我的离线页面将遍历存储在缓存中的页面，并从`localStorage`中检索相应的元数据。

这一切都很好，但是当我一读到[雷米的帖子](https://remysharp.com/2019/09/05/offline-listings)关于他使用的惊人的前额技巧，我就知道我要转换我的代码了。他没有使用`localStorage`——或任何其他浏览器 API——来存储和检索元数据，而是使用页面本身！使用缓存 API，您可以检查已存储页面的内容，并获得您需要的任何信息:

> 我意识到我不需要储存任何东西。 **HTML 就是 API** 。

出于几个原因，重构我的离线页面的代码感觉很好。首先，我能够移除一个依赖项——`localStorage`——并简化 JavaScript。感觉总是很好。但是另一个让我感到温暖模糊的原因是我能够使用数据而不是元数据。

许多年前，科利·多克托罗写了一首名为 [Metacrap](https://people.well.com/user/doctorow/metacrap.htm) 的曲子。在书中，他列举了元数据的许多问题——关于数据的数据。许多问题的根源是元数据与它描述的数据分开存储。数据可能得到更新，而元数据没有相应的更新。元数据往往会腐烂，因为它是看不见的——眼不见心不烦。

事实上，这一直是微格式背后的核心原则之一。不再复制信息——一次作为数据，一次作为元数据——而是重新调整可见的*数据的用途；对其进行标记，使其元信息直接附加到信息本身。*

因此，如果你在网页上有一个人的联系方式，而不是在其他地方重复这些信息——比如在文档的`head`中——你可以附加某种标记来表明哪些可见信息是联系方式。在微格式的情况下，这是通过`class`属性完成的。你可以用来自[的 h-card 微格式](https://indieweb.org/h-card)在已经有你联系信息的页面上做标记。

在我的网站上，我已经用 h-entry 微格式标记了我的博客文章、文章和链接。这些类显式地标注内容说“*这个*是标题”，“*这个*是内容”，等等。这使得其他人更容易重新利用我的内容。例如，如果我回复了别人网站上的一个帖子，并使用[web reference](https://indieweb.org/webmention)ping 他们，他们可以检索我的帖子，并知道哪个位是标题，哪个位是内容，等等。

当我读到 Remy 关于使用缓存 API 直接从缓存页面中检索信息的帖子时，我知道我不必做太多工作。因为我所有的帖子都已经标记了 h-entry 类，所以我可以使用这些钩子来创建一个漂亮的离线页面。

我的离线页面的标记如下:

```
<h1>Offline</h1>
<p>Sorry. It looks like the network connection isn’t working right now.</p>
<div id="history">
</div> 
```

我将用一个名为“pages”的缓存中的信息填充这个“history”`div`,这个缓存是我在服务工作器中使用缓存 API 创建的。

我将使用`async` / `await`来做这件事，因为有许多步骤依赖于前一步的完成。“打开这个缓存，*然后*获取那个缓存的键，*然后*遍历页面，*然后*……”如果没有`async` / `await`，所有这些`then`都会导致一些严重的缩进。

所有的`async`函数都必须有名字——不允许匿名的`async`函数。我把这个叫做`listPages`，就像雷米正在做的一样。我让`listPages`函数立即执行:

```
(async function listPages() {
...
})(); 
```

现在让代码进入那个被立即调用的函数。

我创建了一个名为`browsingHistory`的数组，其中填充了用于“历史”`div`的数据。

 `const browsingHistory = [];` 

稍后我将解析 web 页面，所以我需要一个 DOM 解析器。我给它起了一个富有想象力的名字……`parser`。

 `const parser = new DOMParser();` 

是时候打开我的“页面”缓存了。这是第一个`await`语句。当缓存打开时，这个承诺将得到解决，我将使用变量… `cache`(再次使用富有想象力的命名)访问这个缓存。

 `const cache = await caches.open('pages');` 

现在我得到了缓存的键——那里是所有页面请求的列表。这是第二个`await`。一旦检索到了密钥，我将拥有一个包含所有页面列表的变量。你永远也猜不到我把存储缓存键的变量叫做什么。没错……`keys`！

 `const keys = await cache.keys();` 

是时候开始循环了。我使用一个`for` / `of`循环:
来获取键列表中的每个请求

```
for (const request of keys) {
...
} 
```

在循环内部，我使用缓存 API 的`match()`方法将页面从缓存中取出。我将把我得到的结果存储在一个名为`response`的变量中。和所有涉及缓存 API 的事情一样，这是异步的，所以我需要在这里使用`await`关键字。

 `const response = await cache.match(request);` 

我对响应的标题不感兴趣。我特别寻找 HTML 本身。我可以用`text()`方法得到。同样，它是异步的，我希望在做任何事情之前解决这个承诺，所以我使用了`await`关键字。当承诺完成时，我将拥有一个名为`html`的变量，它包含响应的主体。

 `const html = await response.text();` 

现在我可以使用我之前创建的 DOM 解析器了。我在`html`变量中有一串文本。我可以使用`parseFromString()`方法从该字符串生成一个文档对象模型。这不是异步的，所以不需要`await`关键字。

 `const dom = parser.parseFromString(html, 'text/html');` 

现在我有了一个 DOM，我创造性地将它存储在一个名为… `dom`的变量中。

我可以使用像`querySelector`这样的 DOM 方法来研究它。我可以通过寻找一个具有包含“h-entry”值的`class`属性的元素来测试这个页面是否有 h-entry:

```
if (dom.querySelector('.h-entry h1.p-name') {
...
} 
```

在这个特殊的例子中，我还检查页面的`h1`元素是否是 h-entry 的标题。这样索引页面(比如我的主页)就不会通过这个`if`语句。

在`if`语句中，我将存储从 DOM 中检索到的数据。我将数据保存到一个名为… `data`的对象中！

 `const data = new Object;` 

第一条数据实际上并不在标记中:它是页面的 URL。我可以从我的`for`循环中的`request`变量得到它。

 `data.url = request.url;` 

我将存储这个 h 条目的时间戳。我可以从用`dt-published`类标记的`time`元素的`datetime`属性中得到答案。

 `data.timestamp = new Date(dom.querySelector('.h-entry .dt-published').getAttribute('datetime'));` 

此时，我将从同一个`time.dt-published`元素的`innerText`属性中获取人类可读的日期。

 `data.published = dom.querySelector('.h-entry .dt-published').innerText;` 

h-entry 的标题在类为`p-name`的元素的`innerText`中。

 `data.title = dom.querySelector('.h-entry .p-name').innerText;` 

在这一点上，我实际上将使用一些元捕获来代替可见的 h-entry 内容。我没有在页面的`body`中输出文章的描述，但是我把它放在了`meta`元素的`head`中。我现在就去拿。

 `data.description = dom.querySelector('meta[name="description"]').getAttribute('content');` 

好吧。我得到了一个 URL、一个时间戳、一个出版日期、一个标题和一个描述，所有这些都是从 HTML 中获取的。我将把所有数据放入我的`browsingHistory`数组中。

 `browsingHistory.push(data);` 

我的`if`语句和我的`for` / `in`循环到此结束。下面是整个循环的样子:

```
for (const request of keys) {
  const response = await cache.match(request);
  const html = await response.text();
  const dom = parser.parseFromString(html, 'text/html');
  if (dom.querySelector('.h-entry h1.p-name')) {
    const data = new Object;
    data.url = request.url;
    data.timestamp = new Date(dom.querySelector('.h-entry .dt-published').getAttribute('datetime'));
    data.published = dom.querySelector('.h-entry .dt-published').innerText;
    data.title = dom.querySelector('.h-entry .p-name').innerText;
    data.description = dom.querySelector('meta[name="description"]').getAttribute('content');
    browsingHistory.push(data);
  }
} 
```

这是代码的数据收集部分。现在我要把所有有趣的信息输出到页面上。

首先，我想确保`browsingHistory`数组不是空的。如果是的话，那就没有意义了。

```
if (browsingHistory.length) {
...
} 
```

在这个`if`语句中，我可以对我放入`browsingHistory`数组中的数据做我想做的事情。

我将按出版日期排列这些数据。我不确定这样做是否正确。也许按照您上次访问的顺序显示页面更有意义。我可能会在某个时候删除它，但是现在，我是这样根据数组中每一项的属性对数组进行排序的:

```
browsingHistory.sort( (a,b) => {
  return b.timestamp - a.timestamp;
}); 
```

现在我要连接一些字符串。这是最终将被放入“history”`div`的 HTML 文本字符串。我将标记存储在一个名为… `markup`(我的想象力无限)的字符串中。

 `let markup = '<p>But you still have something to read:</p>';` 

我将为每一项数据添加一大块标记。

```
browsingHistory.forEach( data => {
  markup += `
<h2><a href="${ data.url }">${ data.title }</a></h2>
<p>${ data.description }</p>
<p class="meta">${ data.published }</p>
`;
}); 
```

组装好标记后，我现在可以将它插入到脱机页面的“历史”部分。我正在使用方便的`insertAdjacentHTML()`方法来做这件事。

 `document.getElementById('history').insertAdjacentHTML('beforeend', markup);` 

下面是我完成的 JavaScript 的样子:

```
<script>
(async function listPages() {
  const browsingHistory = [];
  const parser = new DOMParser();
  const cache = await caches.open('pages');
  const keys = await cache.keys();
  for (const request of keys) {
    const response = await cache.match(request);
    const html = await response.text();
    const dom = parser.parseFromString(html, 'text/html');
    if (dom.querySelector('.h-entry h1.p-name')) {
      const data = new Object;
      data.url = request.url;
      data.timestamp = new Date(dom.querySelector('.h-entry .dt-published').getAttribute('datetime'));
      data.published = dom.querySelector('.h-entry .dt-published').innerText;
      data.title = dom.querySelector('.h-entry .p-name').innerText;
      data.description = dom.querySelector('meta[name="description"]').getAttribute('content');
      browsingHistory.push(data);
    }
  }
  if (browsingHistory.length) {
    browsingHistory.sort( (a,b) => {
      return b.timestamp - a.timestamp;
    });
    let markup = '<p>But you still have something to read:</p>';
    browsingHistory.forEach( data => {
      markup += `
<h2><a href="${ data.url }">${ data.title }</a></h2>
<p>${ data.description }</p>
<p class="meta">${ data.published }</p>
`;
    });
    document.getElementById('history').insertAdjacentHTML('beforeend', markup);
  }
})();
</script> 
```

我对此很满意。它不算太长，但仍然很有可读性(我希望如此)。这表明缓存 API 和 h-entry 微格式是天作之合。

如果你已经为你的网站制定了离线策略，并且你正在使用 h-entry 来标记你的内容，请随意使用这些代码。

如果你的网站没有离线策略，[有一本书可以满足你的需求](https://abookapart.com/products/going-offline)。

*这篇文章最初发表于[我自己的网站](https://adactio.com/journal/15844)。*