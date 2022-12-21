# 与 Sapper(苗条)和 Socket 聊天。超正析象管(Image Orthicon)

> 原文：<https://dev.to/tmns/chatting-with-sapper-svelte-and-socket-io-4c6a>

# 上下文

在我的上一个项目中，一个间隔重复学习应用程序(在这里玩演示)，我偶然发现了 [Svelte](https://svelte.dev) ，一个在构建时运行并把你的代码编译成优化的普通 JS 的组件框架。我玩了一下这个教程，它看起来真的很有趣。我也很快发现了 [Sapper](https://sapper.svelte.dev) ，一个用于构建苗条应用的应用框架。所以，我决定我要做的下一个项目将是一些小事情来帮助我了解苗条/工蜂。

除了苗条，我还想测试出[插座。IO](https://socket.io) ，一个支持客户端和服务器之间实时、双向、基于事件的通信的库。因此，我认为将两者结合起来的一个好方法是构建一个简单的聊天应用程序。我还觉得这样的事情不仅是个人学习这些技术的好方法，也是一个可以用来教导他人的例子(例如在研讨会、聚会、课程等中)。因此，我将在下面写一点我是如何发现应用程序构建过程的。对于不耐烦的人，你可以直接去应用程序的[回购](https://github.com/tmns/chat-app)或者玩玩[演示](https://chat-app-svelte.herokuapp.com)。

# 设置

起床和跑步是相当无痛的，真的像工兵医生暗示的那样简单。通过下面的命令序列，我有了一个准备好的新的工蜂项目:

```
npx degit "sveltejs/sapper-template#rollup" chat-app
cd chat-app
npm install 
```

下一步是安装插座。IO 本质。这对于`npm install --save socket.io`来说很容易。然而，我遇到的一个问题是，我无法让我的`index.svelte`正确地从服务器在`/socket.io/socket.io.js`默认公开的独立客户端构建中导入`io`对象。这可能是由于如何加载所有资源的竞争条件。所以，我选择导入包`node_modules`，但是这导致了`bufferutil`的模块未找到错误。

最后，我意识到如果我想走这条路，我还必须显式地安装客户端包:`npm install --save socket.io-client`。虽然入门文档中没有提到这一点，但扩展文档中提到了(doh！！！).另一种选择是尝试从 CDN 加载，但我没有尝试这样做。一旦我安装了客户端包，我就可以将它导入到我的`index.svelte`中，并开始使用`io`对象！

# 构建出服务器

插座。IO 文档很棒，但是它们明确地处理 Express。我很喜欢和了解 Express，但我以前从未尝试过波尔卡，因为它默认带有 Sapper，所以我认为这将是一个尝试的绝佳时机。幸运的是，波尔卡人(或人吗？)还写了一个例子(不过是一个聊天应用！)专门展示如何使用它的是 Socket。木卫一；所以，我可以很大程度上依靠它来获得指导。

点击查看[。这真的是你开始行动所需要的一切。因此，在我解释下面客户机的不同部分时，我将简单地引用相关的服务器代码，而不是在这里赘述。](https://github.com/lukeed/polka/tree/master/examples/with-socketio)

# 构建出客户端

客户端只包含一个 index.svelte 页面和一个`Heading.svelte`组件。我决定将标题抽象成它自己的组件，以防我想在其他页面甚至其他项目中使用它。以苗条的身材达到这样的目标是非常容易的；比如我的`Heading.svelte`长这样:

```
<style>
  [...CSS...]
</style>

<script>
  export let text;
</script>

<div id="heading">{text}</div> 
```

然后，在`index.svelte`中，它是这样使用的:

```
<script>
  import Heading from '../components/Heading.svelte';

  [...]
</script>

<body>
  <div class="main">
    <Heading text={'Chat App'} />
  [...]
</body> 
```

还应该注意的是，你可以为`Heading.svelte`中的`text`设置一个默认值，只要简单地给它赋值，而不是不定义它。

聊天功能本身主要由各种套接字处理。响应用户操作而触发的 IO `socket.on`和`socket.emit`(或`socket.broadcast.emit`)调用。例如，为了实现消息发送，我们首先向应用程序的 send `button`添加一个事件监听器，这可以通过 Svelte 非常容易地实现:

```
<form action="">
  <input id="m" autocomplete="off" {placeholder} bind:value={message} />
  <button on:click|preventDefault={handleSubmit}>Send</button>
</form> 
```

还要注意，我们将消息字段`input`的值绑定到变量`message`。这将创建一个双向绑定，并确保输入到字段中的任何内容都自动绑定到在我们的`script`块中定义的消息变量(通过类似通常的`let message = ''`)。

当用户点击发送`button`时，我们的函数`handleSubmit`，也在我们的`script`块中定义，将被调用。在其中，我们首先将它添加到一个`messages`数组中，该数组充当我们存储给定会话的所有消息的数据结构，然后我们发出一个`'message'`事件:

```
messages = messages.concat(message);
socket.emit('message', message); 
```

然后，在服务器端，我们有一个侦听器，它将侦听这样的事件，并在接收到事件时做出相应的反应。在我们的例子中，这仅仅意味着将消息广播给所有用户(发送消息的用户除外):

```
socket.on('message', function(message) {
  socket.broadcast.emit('message', message);
}) 
```

最后，回到我们的`index.svelte`的`script`块，我们有另一个`socket.on`监听器等待接收这样的`'message'`事件，这样它可以将它们添加到`messages`数组中，并在聊天窗口中显示它们:

```
socket.on('message', function(message) {        
  messages = messages.concat(message);
}); 
```

此时，你可能会想:“好吧，这很好，但是我们如何向用户显示消息呢？”。同样，苗条使这变得非常容易。在 Svelte 的`#each`块功能的帮助下，我们可以简单地通过`ul`创建一个列表元素，然后为`messages`中的每个`message`填充一个`li`元素:

```
<div id="chatWindow">
  <ul id="messages">
    {#each messages as message}
      <li>{message}</li>
    {/each}
  </ul>
</div> 
```

酷，现在每次新的`message`添加到`messages`时，聊天窗口都会更新并即时显示！不过，新信息最好是逐渐消失，而不是突然出现。是时候使用 CSS 或额外的 JavaScript 了，对吗？不，苗条的救援:

```
<li transition:fade>{message}</li> 
```

是的，这就是给每条信息添加一个漂亮的淡入过渡所需要的全部。此外，在这一点上，这是你真正需要有一个工作的聊天客户端！我也可以停在这里，但我想添加一些额外的东西，其中之一是一条消息，通知用户当前总共有多少用户在聊天。

这最终变得有点棘手，因为我不确定在哪里存储和更新这个值。Svelte 本身实现了 app 范围的商店；然而，这被证明不是正确的解决方案，因为我需要在每次新用户连接时为每个客户端更新的东西。因此，最终的解决方案是在服务器端存储值，然后在每个新的`'connection'`事件发生时，更新该值并将其发送回客户端，然后客户端相应地更新其本地副本。首先是服务器代码:

```
[...]
let numUsers = 0;

io(server).on('connection', function(socket) {
  ++numUsers;
  let message = 'Server: A new user has joined the chat';
  socket.emit('user joined', { message, numUsers });
  socket.broadcast.emit('user joined', { message, numUsers });
[...] 
```

注意，我们必须调用`socket.emit`(向发送事件的用户发回消息)和`socket.broadcast.emit`(向除发送事件的用户之外的所有用户发送消息)。这是因为我们希望加入的用户和所有其他用户都能收到该事件，并在本地存储正确的用户数量。

然后，回到客户端的`script`块中，我们设置局部变量来存储连接的用户数量和`'user joined'`事件的侦听器:

```
let numUsersConnected = 0;
[...]
socket.on("user joined", function({message, numUsers}) {
  messages = messages.concat(message);
  numUsersConnected = numUsers;
  updateScroll();
});
[...] 
```

最后，在我们的`html`中，我们插入这样的数据:

```
<p>There {numUsersConnected == 1 ? 'is' : 'are'} {numUsersConnected} {numUsersConnected == 1 ? 'user' : 'users'} currently chatting!</p> 
```

您可能已经注意到，在上面的监听器中，我们还调用了一个名为`updateScroll`的函数。这只是一个在每个新消息上自动滚动聊天窗口的功能:

```
function updateScroll() {
  const chatWindow = document.getElementById('chatWindow');
  chatWindow.scrollTop = chatWindow.scrollHeight;   
} 
```

我遇到的问题是，每次调用这个函数时，聊天窗口都会自动滚动，但不会一直滚动到`div`的底部。取而代之的是，它会滚动到上一条消息的上方，所以用户总是需要多滚动一点才能显示最新的消息。是什么导致了这个奇怪的错误？？

我终于意识到，这是由于一个小的比赛条件。本质上，updateScroll 函数在新消息被添加到 DOM 之前被调用*，导致`scrollTop`总是被设置在最新消息的上面！为了解决这个问题，我简单地将`scrollTop`赋值放入一个`setTimeout`回调中，这样它将由 web API 处理，并被迫在`job queue`中等待，直到所有其他代码都被执行:*

```
function updateScroll() {
  const chatWindow = document.getElementById('chatWindow');
  setTimeout(() => {
    chatWindow.scrollTop = chatWindow.scrollHeight; 
  }, 0);
} 
```

就这样，卷轴开始完美地工作了！

在使用 Svelte 时，您可能会遇到的最后一个问题是，如果您试图向`window`对象添加一个事件监听器，那么在执行时您将得到一个`ReferenceError: window is not defined`。这也是由于竞争条件；然而，Svelte 记录了这一点，并提供了一种通过特殊的`svelte:window`元素与`window`对象交互的简单方法。在这个应用程序中，每当窗口卸载时，元素被用来调用一个函数:

```
<svelte:window on:unload={emitUserDisconnect} 
```

就这样，您不仅可以访问`window`对象，还可以轻松地设置事件监听器！

# 结论

这是一个真正快速而有趣的入门项目，可以让你熟悉 Svelte / Sapper 和 Socket.IO。我认为在一个下午的知识分享会议(如 meetup)中，它将成为一个与他人一起实施的伟大项目。虽然肯定有一些 gotchyas，但是一旦你真正理解了问题，你通常会发现它们实际上已经被记录下来，并且存在一个干净的解决方案。建议你试试这些技术；两者都使开发变得相当轻松，也许更重要的是——非常有趣！