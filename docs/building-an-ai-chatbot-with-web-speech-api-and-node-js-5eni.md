# 用 Web Speech API 和 Node.js 构建人工智能聊天机器人

> 原文：<https://dev.to/giladmaayan/building-an-ai-chatbot-with-web-speech-api-and-node-js-5eni>

[![alt Pexels](img/678929417dab16685036e0d26fd3871d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sqiR0t_c--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.pexels.com/photos/190362/pexels-photo-190362.jpeg%3Fauto%3Dcompress%26cs%3Dtinysrgb%26dpr%3D3%26h%3D750%26w%3D1260)

<figcaption>

<figure>

<figcaption></figcaption>

</figure>

</figcaption>

Web Speech API 允许您将语音数据整合到 Web 应用程序中。Web 语音 API 有两个部分:语音合成(文本到语音)和语音识别(异步语音识别)。)本教程将介绍使用 Web Speech API 和 Node.js 在 Web 浏览器中创建基于 AI 的语音聊天 UI 时要遵循的步骤。一旦完成，该应用程序应该能够理解用户的声音，并使用合成语音进行适当的回复。

虽然 Web Speech API 在 Chrome 和 Firefox 中已经存在了一段时间，但该功能在 Safari 和其他浏览器中仅部分可用。例如，Safari 只支持语音合成，不支持语音识别。可以查看[支持的浏览器](https://developer.mozilla.org/en-US/docs/Web/API/Web_Speech_API)列表。

构建 web 应用程序需要我们经历三个主要步骤

1.  使用 Web 语音 API 中的语音识别界面来听用户的声音
2.  将用户的消息作为文本串传输到商业自然语言处理 API
3.  使用语音合成接口将合成语音借给从 API.ai 接收的响应文本

此外，本教程的完整源代码可以在 Github 上找到。

本教程大量使用 Node.js。熟悉 JavaScript 并对 Node.js 有基本的了解是很重要的。

## 设置 Node.js 应用程序

首先，使用 Node.js 设置一个 web 应用程序框架。您可以创建您的应用程序目录并构建您的应用程序结构，如下所示—

```
├── index.js
├── public
│   ├── css
│   │   └── style.css
│   └── js
│       └── script.js
└── views
    └── index.html 
```

在这篇文章中，我们将保持事情简单。您还可以尝试 Express generator，它为一个完整的应用程序创建了一个框架，其中包含许多 JavaScript 文件、Jade 模板和用于各种目的的子目录。

要初始化 Node.js 应用程序，现在可以运行下面的命令-

```
$ npm init -f 
```

*–f*标志确保默认设置被接受。如果没有，您可以手动配置没有标志的应用程序。这应该还会生成一个 *package.json* 文件。这包含有关您的应用程序的必需基本信息。完成后，你就可以安装构建你的应用所需的依赖项了—

```
$ npm install express Socket.io apiai --save 
```

一旦添加了 *- save* 标志，您的 *package.json* 文件就会自动更新相关的依赖项。

```
{
 "name": "ai-bot",
 "version": "1.0.0",
 "description": "",
 "main": "index.js",
 "dependencies": {
   "apiai": "^4.0.3",
   "express": "^4.16.3",
   "Socket.io": "^2.1.1"
 },
 "devDependencies": {},
 "scripts": {
   "test": "echo \"Error: no test specified\" && exit 1"
 },
 "keywords": [],
 "author": "",
 "license": "ISC"
} 
```

为了在本地运行服务器，我们将使用 Express，一个 Node.js web 应用服务器框架。Express 帮助你编写中间件，这是制作服务器的一个很好的模式。为了允许浏览器和服务器之间的实时双向通信，我们将使用 Socket.io。对于外行来说，它本质上是一个库，允许用户使用 Node.js 有效地使用 WebSocket。

拼图的最后一块是 Dialogflow 的遗留 Node.js 客户端，名为[apai](https://github.com/dialogflow/dialogflow-nodejs-client)，它将帮助我们的语言机器人变得智能。因为它简单、容易和直观，你将不需要任何[自然语言处理](https://medium.com/@ageitgey/natural-language-processing-is-fun-9a0bff37854e)或深度学习专业知识。

通过在服务器和客户端之间建立套接字连接，一旦 Web Speech API 或 API.ai API 返回文本数据，就会传输聊天消息。

下一步是创建一个 *index.js* 文件，然后实例化 Express。
这是我们快速服务器的基本版本——

```
const express = require('express')
const app = express()
const port = 3000

app.use(express.static(__dirname + '/views')); // html
app.use(express.static(__dirname + '/public')); // js, css, images
//Any request to '/' renders index.html
app.get('/', (req, res) => {
  res.sendFile('index.html');
});

//Start listening to the port
app.listen(port, () => console.log(`Example app listening on port ${port}!`)) 
```

## 用语音识别接口接收语音

语音识别是 Web 语音 API 的主要控制器接口。它用于通过麦克风接收用户的语音。有

### 创建用户界面

我们试图建立的应用程序有一个相对简单的用户界面。它基本上由一个触发语音识别的按钮组成。

首先，我们将需要设置我们的“index.html”文件，然后包括前端 JavaScript 文件、 *script.js* 和 Socket.io。这将在稍后阶段使用，以允许实时通信—

```
<html lang="en">
  <head>...</head>
  <body>
    ...
    <script src="https://cdnjs.cloudflare.com/ajax/libs/Socket.io/2.0.1/Socket.io.js"></script>
    <script src="js/script.js"></script>
  </body>
</html> 
```

接下来可以在 HTML
的主体中添加一个按钮接口

```
<button>Talk</button> 
```

## 用 JavaScript 捕捉语音

从 *script.js* 中，您可以调用一个语音识别实例。您可以使用 Web 语音 API 的控制器接口来管理语音识别-

```
const SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;

const recognition = new SpeechRecognition(); 
```

由于 Chrome 目前支持带前缀属性的 API，因此带前缀和不带前缀的对象都被包含在内。

此外，这里使用了 *ECMAScript6* 语法，包括*常量*和箭头函数，因为这些功能目前在支持两种语音 API 接口的浏览器中都可用—*语音识别*和*语音合成*

用户还可以选择设置不同的属性来定制语音识别–

```
recognition.lang = 'en-US';
recognition.interminResults = false; 
```

一旦完成，就可以捕获按钮 UI 的 DOM 引用，并且可以监听表示语音识别开始的点击事件-

```
document.querySelector('button').addEventListener('click', () => {
  recognition.start();
}); 
```

语音识别开始后，您可以使用*结果*事件来请求文本内容-

```
recognition.addEventListener('result', (e) => {
  let last = e.results.length - 1;
  let text = e.results[last][0].transcript;

  console.log('Confidence: ' + e.results[0][0].confidence);

  // We will use the Socket.io here later...
}); 
```

这段代码将显示一个`SpeechRecognitionResultList`对象。这将包含可以从数组中检索的结果。

下一步，我们将使用 Socket.io 将结果传输到服务器代码。

## 与 Socket.io 实时通信

Socket.io 本质上是一个用于基于 web 的实时应用程序的库。它允许服务器和 web 客户端之间的实时双向通信。这里，我们将尝试使用 Socket.io 将结果从浏览器传递到 Node.js 代码，然后将响应传递回我们的浏览器。

我们通过 Socket.io 而不是 HTTP 或 AJAX 来使用 WebSocket，因为当涉及到双向通信时，尤其是在浏览器和服务器之间推送事件时，套接字是首选的解决方案。使用连续连接，我们不需要频繁地重新加载浏览器或发送 AJAX 请求。

接下来，可以在*script . js*–
中实例化 Socket.io

```
const socket = io(); 
```

接下来，我们在通过*语音识别*
播放结果事件的地方插入这段代码

```
socket.emit('chat message', text); 
```

现在，我们返回 Node.js 来接收文本，并使用 AI 来表达对用户的回复。

## 将人工智能与我们的应用集成

许多不同的服务和平台允许通过语音到文本和自然语言处理将应用程序与人工智能系统集成。其中包括微软的 LUIS、IBM 的 Watson 和 Wit.ai，还有更复杂的深度学习平台用于要求更高的项目。

为了简化本教程，我们将使用 DialogFlow，因为它提供了一个免费的开发人员帐户，并允许通过其 Node.js 库和 web 界面轻松设置一个聊天系统。如果你以前没有听说过 DialogFlow，这里有一段来自维基百科的摘录:

Dialogflow(以前的 API.ai，Speaktoit)是谷歌旗下的基于自然语言对话的人机交互技术开发商。该公司最出名的是创造了 Assistant(由 Speaktoit 提供)，这是一个适用于 Android、iOS 和 Windows Phone 智能手机的虚拟好友，可以用自然语言执行任务并回答用户的问题。[1] Speaktoit 还创建了一个自然语言处理引擎，它结合了对话历史、位置和用户偏好等对话上下文。

## 设置对话流

要设置 DialogFlow，您需要创建一个 [DialogFlow 帐户](https://dialogflow.com/docs/getting-started/create-account)。

创建帐户后，您需要创建一个“代理”。*入门*指南说明了所有相关细节。

填写代理的详细信息。

不需要选择完整的定制方法并创建实体和意图，您只需点击左侧菜单中的*闲聊*。

然后，您可以切换开关以启用该服务。

您可以选择通过 DialogFlow 界面根据您的偏好定制您的闲聊代理。你可以这样做:

要将 API 用于我们的 Nodejs 应用程序，您需要转到“常规设置”页面(单击菜单中您的代理名称旁边的 cog 图标)并检索您的 API 密钥。使用 Node.js SDK 将需要“客户端访问令牌”。确保你选择了 *v1* 而不是 *v2* 。尽管*v2*API 越来越成熟，出于演示的目的，我们还是坚持使用 *v1* 。

## 使用 DialogFlow 节点。Js SDK

要通过 DialogFlow 的 Node.js SDK 将 Node.js 应用程序连接到 dialog flow，您需要返回到“index.js”文件。您需要使用您的访问令牌-
来初始化 API.ai

```
const apiai = require('apiai')(APIAI_TOKEN); 
```

如果您喜欢在本地执行代码，那么您需要在这个阶段硬编码您的 API 密匙。有许多不同的方法来设置您的环境变量。这里我们设置了一个*。env* 文件来包含这些变量。

我们现在使用服务器端的 Socket.io 从浏览器接收结果。在连接完成并且接收到所需的消息后，您可以使用 DialogFlow APIs 调用用户对消息的回复-

```
io.on('connection', function(socket) {
  socket.on('chat message', (text) => {

    // Get a reply from API.ai

    let apiaiReq = apiai.textRequest(text, {
      sessionId: APIAI_SESSION_ID
    });

    apiaiReq.on('response', (response) => {
      let aiText = response.result.fulfillment.speech;
      socket.emit('bot reply', aiText); // Send the result back to the browser!
    });

    apiaiReq.on('error', (error) => {
      console.log(error);
    });

    apiaiReq.end();

  });
}); 
```

一旦 API.ai 生成结果，就可以使用 Socket.io 的 *socket.emit()* 函数推送到浏览器。

## 用语音合成接口给 AI 一个声音

现在是时候返回“script.js”并完成应用程序了。
首先创建一个函数来生成合成语音。这里我们使用来自 Web 语音 API 的*语音合成*控制器接口。该函数选取一个字符串作为参数，然后允许浏览器朗读文本–

```
function synthVoice(text) {
  const synth = window.speechSynthesis;
  const utterance = new SpeechSynthesisUtterance();
  utterance.text = text;
  synth.speak(utterance);
} 
```

该功能有三个步骤

1.  为 API 入口点–*window . speech synthesis*创建一个引用。这不需要带前缀的属性，因为与 SpeechRecognition 相比，这个特定的 API 得到了广泛的支持。
2.  在下一步中，我们通过其构造函数创建一个新的*speechsynthesisatinterance()*实例。需要合成的相关文本现在已经设置好了。您还可以选择设置其他属性，包括选择浏览器和操作系统支持的语音集的语音。
3.  在最后一步，我们使用 *SpeechSynthesis.speak()* 让它说话。现在可以使用 Socket.io 从服务器检索响应。一旦检索到此消息，您就可以调用以下函数

```
socket.on('bot reply', function(replyText) {
    synthVoice(replyText);
}); 
```

你现在应该准备好测试你的人工智能聊天机器人了。

重要的是要记住，您的浏览器在使用麦克风之前会首先请求许可。与通知 API 和地理位置 API 等其他 web APIs 类似，除非明确授予权限，否则浏览器不会访问敏感信息。