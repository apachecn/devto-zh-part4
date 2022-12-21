# 网络工作者的实时处理

> 原文：<https://dev.to/bnevilleoneill/real-time-processing-with-web-workers-2hjg>

[![](img/f02eca64c46e411658cdf1a2aadd52da.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8D-q5eto--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rt36pdou6yawrpjeojdh.png)

作为一名 JavaScript 开发人员，你应该已经知道它的[单线程处理模型](https://developer.mozilla.org/en-US/docs/Web/JavaScript/EventLoop):你所有的 JavaScript 代码都在单线程内执行。甚至事件处理和异步回调都是在同一个线程中执行的，并且多个事件是依次处理的，一个接一个。换句话说，普通 JavaScript 代码的执行没有并行性。

这听起来可能很奇怪，因为这意味着 JavaScript 代码没有充分利用机器的计算能力。此外，当一段代码运行时间过长时，这种模型可能会导致一些问题。在这种情况下，您的应用程序可能会变得没有响应。

幸运的是，最近的 web 浏览器提供了一种方法来克服这个潜在的性能问题。HTML5 规范引入了 [Web Workers](https://html.spec.whatwg.org/multipage/workers.html#workers) 在浏览器端提供 JavaScript 计算的并行性。

在这篇文章中，我们将说明如何使用 Web Workers。我们将构建一个简单的文本分析器，并逐步增强其实现，以避免 JavaScript 单线程处理模型导致的性能问题。

[![](img/7f6a42d29b425a7ee85911b23f8c0618.png)](https://logrocket.com/signup/)

## 构建实时文本分析器

我们的目标是实现一个简单的应用程序，当用户在文本区域输入文本时，显示一些关于文本的统计数据。

应用程序的 HTML 标记如下所示:

```
<textarea id="text" rows="10" cols="150" placeholder="Start writing...">
</textarea>

<div>
  <p>Word count: <span id="wordCount">0</span></p>
  <p>Character count: <span id="charCount">0</span></p>
  <p>Line count: <span id="lineCount">0</span></p>
  <p>Most repeated word: <span id="mostRepeatedWord"></span> (<span id="mostRepeatedWordCount">0</span> occurrences)</p>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

您可以看到一个 *textarea* 元素，用户可以在其中输入文本，还可以看到一个 div 元素，应用程序在其中显示关于插入文本的统计数据，比如字数、字符数、行数和重复次数最多的单词。请记住，这些数据是在用户书写时实时显示的。

提取并显示统计数据的相关 JavaScript 代码如下:

```
const text = document.getElementById("text");
const wordCount = document.getElementById("wordCount");
const charCount = document.getElementById("charCount");
const lineCount = document.getElementById("lineCount");
const mostRepeatedWord = document.getElementById("mostRepeatedWord");
const mostRepeatedWordCount = document.getElementById("mostRepeatedWordCount");

text.addEventListener("keyup", ()=> {
  const currentText = text.value;

  wordCount.innerText = countWords(currentText);
  charCount.innerText = countChars(currentText);
  lineCount.innerText = countLines(currentText);
  let mostRepeatedWordInfo = findMostRepeatedWord(currentText);
  mostRepeatedWord.innerText = mostRepeatedWordInfo.mostRepeatedWord;
  mostRepeatedWordCount.innerText = mostRepeatedWordInfo.mostRepeatedWordCount;
}); 
```

Enter fullscreen mode Exit fullscreen mode

在这里，您可以看到一个语句块获取显示数据所涉及的各种 DOM 元素，以及一个事件侦听器在用户完成按下每个键时捕获这些数据。

在 *keyup* 事件监听器的主体中，您会发现一些对执行实际数据分析的函数的调用:countWords()、countChars()、countLines()和 findMostRepeatedWord()。你可以在 [CodePen](https://codepen.io/andychiare/pen/eowYvx) 上找到这些功能的实现以及文本分析器的整体实现。

## 单线程的性能问题

通过分析这个简单的文本分析器应用程序的源代码，您可以看到，每当用户按完键盘上的一个键时，就会执行统计提取。当然，与数据提取相关的计算工作量取决于文本的长度，因此当文本变大时，性能可能会有所下降。

考虑到本例中考虑的文本分析功能非常简单，但是您可能想要提取更复杂的数据，比如关键词及其相关性、单词分类、平均句子长度等等。即使对于短或中等长度的文本，该应用程序可能执行得很好，但对于长文本，您可能会体验到性能损失，并使应用程序变得没有响应，特别是当它在低性能设备(如智能手机)中执行时。

## 网络工作者基础知识

单线程处理模型是 [JavaScript 语言规范](https://tc39.github.io/ecma262/#sec-jobs-and-job-queues)中固有的，它既适用于浏览器也适用于服务器。为了克服这种语言限制， [HTML5 规范](https://html.spec.whatwg.org/multipage/workers.html)引入了 *worker* 概念，这是一个对象，提供了一种在独立线程中执行 JavaScript 代码的方法。

创建一个 worker 很简单:您所需要的就是将您想要执行的代码隔离在一个文件的单独线程中，并通过调用 _ worker()_ 构造函数创建一个 Worker 对象，如下面的示例所示:

```
const myWorker = new Worker(“myWorkerCode.js”); 
```

Enter fullscreen mode Exit fullscreen mode

这种类型的工作者被称为 Web 工作者(另一种类型的工作者是服务工作者，但这超出了本文的范围)。

主线程和工作线程之间的交互基于消息交换系统。主线程和工作线程都可以使用 postMessage()方法发送消息，并通过处理 Message 事件接收消息。

例如，主线程可以通过发送如下消息来启动工作线程:

```
myWorker.postMessage(“start”); 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，我们传递了 _start> *字符串作为 _postMessage()* 的参数，但是您可以传递您想要的任何内容。这取决于你和你的 Web 工作者的期望，但是记住，你不能传递函数。但是，请记住，数据是通过值传递的。因此，如果你传递一个对象，它将被[克隆](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API/Structured_clone_algorithm)，并且工人对它的任何改变都不会影响原始对象。

工人通过实现*消息*事件的监听器来接收消息，如下所示:

```
self.addEventListener(“message”, (event) => {
  if (event.data === “start”) {
    //do things
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

你可以注意到 self 关键字。它指的是当前的工作上下文，不同于主线程的全局上下文。您也可以使用 *this* 关键字来引用 worker 上下文，但是按照惯例，self 通常是首选的。

因此，在上面的示例中，您将事件侦听器附加到当前工作上下文，并通过 event.data 属性访问来自主线程的数据。

同样，工人可以通过使用 postMessage():
向主线程发送消息

```
self.postMessage(“ok”); 
```

Enter fullscreen mode Exit fullscreen mode

主线程通过处理消息事件来接收消息，比如:

```
myWorker.addEventListener(“message”, (event) => {
  if (event.data === “ok”) {
    //do things
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

注意，一个工作线程可能会创建另一个工作线程并与之通信，因此交互并不局限于一个工作线程和主线程。

最后，您可以通过两种方式显式停止一个工作线程:从工作线程内部调用 self.close()和从调用线程使用 terminate()方法，如下例所示:

```
myWorker.terminate(); 
```

Enter fullscreen mode Exit fullscreen mode

## 一个文本分析器的 Web 工作人员

在探索了 Web Workers 的基础知识之后，让我们将它们应用到我们的应用程序中。

首先，让我们提取代码放在一个名为 textAnalyzer.js 的单独文件中。您可以通过定义一个函数 analyze()并返回文本分析的结果来重构代码，如下所示:

```
function analyze(str) {
  const mostRepeatedWordInfo = findMostRepeatedWord(str);

  return {
    wordCount: countWords(str),
    charCount: countChars(str),
    lineCount: countLines(str),
    mostRepeatedWord: mostRepeatedWordInfo.mostRepeatedWord,
    mostRepeatedWordCount: mostRepeatedWordInfo.mostRepeatedWordCount
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

其他函数 countWords()、countChars()等都是在同一个 textAnalyzer.js 文件中定义的。

在同一个文件中，我们需要处理消息事件，以便与主线程进行交互。下面是需要的代码:

```
self.addEventListener("message", (event) => {
  postMessage(analyze(event.data));
}); 
```

Enter fullscreen mode Exit fullscreen mode

事件侦听器期望在事件对象的数据属性中分析文本。它唯一的任务就是通过 postMessage()返回对文本应用 analyze()函数的结果。

现在，主脚本中的 JavaScript 代码变成如下:

```
const text = document.getElementById("text");
const wordCount = document.getElementById("wordCount");
const charCount = document.getElementById("charCount");
const lineCount = document.getElementById("lineCount");
const mostRepeatedWord = document.getElementById("mostRepeatedWord");
const mostRepeatedWordCount = document.getElementById("mostRepeatedWordCount");

const textAnalyzer = new Worker("textAnalyzer.js");

text.addEventListener("keyup", ()=> {
  textAnalyzer.postMessage(text.value);  
});

textAnalyzer.addEventListener("message", (event) => {
  const textData = event.data;

  wordCount.innerText = textData.wordCount;
  charCount.innerText = textData.charCount;
  lineCount.innerText = textData.lineCount;
  mostRepeatedWord.innerText = textData.mostRepeatedWord;
  mostRepeatedWordCount.innerText = textData.mostRepeatedWordCount;
}); 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，我们基于 textAnalyzer.js 文件创建了 *textAnalyzer* Web Worker。

每当用户输入一个键时，就会通过 postMessage()向工作人员发送一条包含完整文本的消息。来自 worker 的响应来自对象形式的 event.data，其属性值被分配给相应的 DOM 元素进行显示。

由于 Web Worker 的代码是在一个单独的线程中执行的，用户可以在文本分析进行的同时继续插入新的文本，而不会遇到无响应的情况。

## 处理错误

如果在 worker 执行过程中出现错误，会发生什么？在这种情况下，会触发一个错误事件，您应该通过一个普通的事件侦听器在调用线程中处理它。

例如，假设我们的文本分析器工作人员检查消息中传递的数据是否确实是文本，如下面的代码所示:

```
self.addEventListener("message", (event) => {
  if (typeof event.data === "string") {
    postMessage(analyze(event.data));    
  } else {
    throw new Error("Unable to analyze non-string data");
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

侦听器在分析传递的数据并将消息发送到主线程之前，确保它是一个字符串。如果传递的数据不是文本，则会引发异常。

在主线程端，您应该通过为错误事件实现一个监听器来处理这个异常，如下所示:

```
textAnalyzer.addEventListener("error", (error) => {
  console.log(`Error "${error.message}" occurred in the file ${error.filename} at line ${error.lineno}`);
}); 
```

Enter fullscreen mode Exit fullscreen mode

事件处理程序接收一个 error 对象，其中包含一些关于出错原因的数据。在我们使用的示例中:

*   *消息*属性描述了发生的错误，
*   *filename* 属性报告实现工作器的脚本文件的名称
*   *lineno* 属性包含发生错误的行号

你可以通过[这个链接](https://github.com/andychiare/simple-text-analyzer-web-worker/tree/master/dedicated-worker)找到这个实现的完整代码。

## 网络工作者限制

我希望您同意 Web Workers 非常棒，而且使用起来非常简单:您只需要使用普通的 JavaScript 和标准事件处理来实现线程间的互操作。没什么特别奇怪或复杂的。

但是，请记住，网络工作者有一些限制:

*   他们不能访问 DOM，无论是*窗口*还是*文档*对象。所以，举例来说，不要试图使用 *console.log()* 在浏览器的控制台上打印消息。这种限制以及传递序列化消息数据是使 Web Workers 线程安全所必需的。乍一看，这似乎限制太多，但实际上，这种限制引导你更好地分离关注点，一旦你学会了如何与员工打交道，好处就显而易见了。
*   此外，只有当应用程序的文件通过 HTTP 或 HTTPS 协议提供时，Web Workers 才会运行。换句话说，如果您的页面是通过 *file://* 协议从本地文件系统加载的，它们就不会运行。
*   最后，同样的原产地政策也适用于网络工作者。这意味着实现 worker 的脚本必须与调用脚本来自同一个域，包括协议和端口。

## 共享工作者

如前所述，Web 工作者被用来实现昂贵的处理任务，以便分配计算负载。有时，网络工作者可能需要大量的资源，例如内存或本地存储。当打开来自同一应用程序的多个页面或框架时，这些资源会为 Web Worker 的每个实例复制。如果 worker 的逻辑允许，您可以通过在多个浏览器上下文中共享 Web worker 来避免不断增长的资源请求。

*共享工作者*可以帮助你。他们是我们目前所见的网络工作者的变体。为了将这种变体类型与之前的区别开来，后者通常被称为*专用工人*。

让我们看看如何通过转换我们的文本分析器来创建一个共享的工作器。

第一步是使用 *SharedWorker()* 构造函数，而不是 *Worker()* :

```
const textAnalyzer = new SharedWorker("textAnalyzer.js"); 
```

Enter fullscreen mode Exit fullscreen mode

此构造函数为工作线程创建一个代理。由于 worker 将与多个调用者通信，代理将有一个专用端口，必须使用该端口来连接侦听器和发送消息。因此，您需要为消息事件附加一个监听器，如下所示:

```
textAnalyzer.port.addEventListener("message", (event) => {
  const textData = event.data;

  wordCount.innerText = textData.wordCount;
  charCount.innerText = textData.charCount;
  lineCount.innerText = textData.lineCount;
  mostRepeatedWord.innerText = textData.mostRepeatedWord;
  mostRepeatedWordCount.innerText = textData.mostRepeatedWordCount;
}); 
```

Enter fullscreen mode Exit fullscreen mode

请注意，唯一的区别是使用了 port 属性来附加事件侦听器。同样，您需要使用 port 属性通过 postMessage():
发送消息

```
text.addEventListener("keyup", ()=> {
  textAnalyzer.port.postMessage(text.value);
}); 
```

Enter fullscreen mode Exit fullscreen mode

然而，与之前不同的是，您需要通过调用 start()方法显式地将您的线程连接到工作线程，如下所示:

```
textAnalyzer.port.start(); 
```

Enter fullscreen mode Exit fullscreen mode

这是确保在添加侦听器之前端口不会调度事件所必需的。但是，请记住，如果您将侦听器附加到 *onmessage* 属性，而不是使用 addEventListener()，则不需要调用 start() ，就像下面这样:

```
textAnalyzer.port.onmessage = (event) => {
  const textData = event.data;

  wordCount.innerText = textData.wordCount;
  charCount.innerText = textData.charCount;
  lineCount.innerText = textData.lineCount;
  mostRepeatedWord.innerText = textData.mostRepeatedWord;
  mostRepeatedWordCount.innerText = textData.mostRepeatedWordCount;
}; 
```

Enter fullscreen mode Exit fullscreen mode

在 worker 端，您需要通过用下面的代码替换消息事件监听器来安排 worker 设置的位:

```
self.addEventListener("connect", (event) => {
  const port = event.ports[0];

  port.addEventListener("message", (event) => {
    if (typeof event.data === "string") {
      port.postMessage(analyze(event.data));    
    } else {
      throw new Error("Unable to analyze non-string data");
    }
  });

  port.start();
}); 
```

Enter fullscreen mode Exit fullscreen mode

您为连接事件添加了一个侦听器。当调用方调用工作代理端口的 start()方法，或者将事件侦听器附加到 *onmessage* 属性时，将触发此事件。在这两种情况下，一个端口被分配给 worker，您可以通过访问 event 对象的 ports 数组的第一个元素来获得它。与调用者类似，您需要使用这个端口来附加事件侦听器和发送消息。此外，如果您使用 addEventListener()附加您的侦听器，您需要通过 port.start()方法建立与调用者的连接。

现在，您的员工已成为共享员工。

这个实现的完整代码可以在[这个链接](https://github.com/andychiare/simple-text-analyzer-web-worker/tree/master/shared-worker)获得。

## 结论

在本文中，我们讨论了 JavaScript 单线程处理模型在某些场景中的局限性。一个简单的实时文本分析器的实现试图更好地解释这个问题。

引入 Web Workers 是为了解决潜在的性能问题。它们被用来在一个单独的线程中产卵。我们讨论了 Web Worker 的限制，最后解释了当我们需要在多个页面或框架之间共享一个 Web Worker 时，如何创建共享 Worker。

您可以在这个 GitHub 存储库中找到本文中创建的 workers 的最终代码。

* * *

## Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![LogRocket Dashboard Free Trial Banner](img/441375fdfee1c124c713d0800bb5e0d5.png)](https://logrocket.com/signup/)

[log rocket](https://logrocket.com/signup/)是一个前端日志工具，让你重放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 动作和状态，LogRocket 还记录控制台日志、JavaScript 错误、stacktraces、带有头+体的网络请求/响应、浏览器元数据、自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。

* * *

帖子[与网络工作者的实时处理](https://blog.logrocket.com/real-time-processing-web-workers/)首先出现在[博客](https://blog.logrocket.com)上。