# 使用管道服务器，仅通过 HTTP 进行简单聊天，没有 WebSocket 和 WebRTC

> 原文：<https://dev.to/nwtgck/simple-chat-over-only-http-without-websocket-and-webrtc-1n4m>

大家好！我将向您展示一个使用管道服务器通过 HTTP/HTTPS 进行的简单文本聊天，这是在通过 HTTP/HTTPS 的每个设备之间的[数据流中引入的。](https://dev.to/nwtgck/data-streaming-with-every-device-over-httphttps-mo4)

## 演示视频

这是一个简单的聊天演示视频。
[https://www.youtube.com/embed/WeLkjg7nsvY](https://www.youtube.com/embed/WeLkjg7nsvY)

## 如何在 JavaScript 中使用管道服务器？

管道服务器的目的是数据传输。这里有一个用法。通过 POST 方法发送，通过 Get 方法获取。

```
// Send
fetch("https://ppng.io/mytext", {
  method: "POST",
  body: 'hello, world'
})

// Get
const res = await fetch("https://ppng.io/mytext")
await res.text() // === 'hello, world' 
```

Enter fullscreen mode Exit fullscreen mode

可以自由更改`/mytext`和 [https://ppng.io](https://ppng.io) 。运行管道服务器的一个简单方法是使用 Heroku。可以点击 GitHub 上[管道服务器上的【部署到 Heroku】。](https://github.com/nwtgck/piping-server)

通过使用这个，您可以构建一个简单的聊天，而不需要 WebSocket 或 WebRTC。

## 试用 CodePen

代号:[https://codepen.io/nwtgck/pen/xNoKgx](https://codepen.io/nwtgck/pen/xNoKgx)

[https://codepen.io/nwtgck/embed/xNoKgx?height=600&default-tab=result&embed-version=2](https://codepen.io/nwtgck/embed/xNoKgx?height=600&default-tab=result&embed-version=2)

[https://codepen.io/nwtgck/embed/xNoKgx?height=600&default-tab=result&embed-version=2](https://codepen.io/nwtgck/embed/xNoKgx?height=600&default-tab=result&embed-version=2)

两个密码笔是一样的。

## 全码

这是聊天的全部代码。

```
<html>
<head>
  Simple Chat via Piping Server
  <style>
    .me {
      text-align: right;
    }
  </style>
</head>
<body>
  <p>
    <input placeholder="Your ID" id='your_id'>
    <input placeholder="Peer ID" id='peer_id'>
    <button onclick='receiveLoop(this)'>Connect</button>
  </p>
  <p style='position: absolute; bottom: 0;'>
    <input placeholder="Message" id='message' size='50'>
    <button onclick="send()">Send</button>
  </p>

  <hr>
  <div id='talks'>
    <!--This will be added by JavaScript -->
  </div>
  <script>
    // Receive-loop
    async function receiveLoop(btn) {
      your_id.disabled = peer_id.disabled = btn.disabled = true;
      while(true) {
        try {
          // Get peer's response
          const res = await fetch(`https://ppng.io/${peer_id.value}-${your_id.value}`);
          // Create talk element
          const talk = document.createElement('div');
          // Set peer's message
          talk.innerText = await res.text();
          // Add peer's message
          talks.appendChild(talk);
        } catch(err) {
          console.error(err);
        }
      }
    }

    // Send your message
    function send() {
      // Send your message
      fetch(`https://ppng.io/${your_id.value}-${peer_id.value}`, {
        'method': 'POST',
        body: message.value
      });
      // Create talk element
      const talk = document.createElement('div');
      talk.innerText = message.value;
      talk.classList.add('me');
      talks.appendChild(talk);
      // Empty your message
      message.value = '';
    }
  </script>
</body>
<html> 
```

Enter fullscreen mode Exit fullscreen mode

## 通过管道服务器进行安全聊天

你可以在下面找到更安全的聊天。应用程序是用 Vue 的 TypeScript 编写的。
GitHub:[https://github.com/nwtgck/piping-chat-web](https://github.com/nwtgck/piping-chat-web)

这些功能如下。

*   AES 端到端加密 [GCM](https://en.wikipedia.org/wiki/Galois/Counter_Mode)
*   [转发保密](https://en.wikipedia.org/wiki/Forward_secrecy)由 [ECDH](https://en.wikipedia.org/wiki/Elliptic-curve_Diffie%E2%80%93Hellman)
*   像 SSH 这样的公钥认证
*   通过[管道服务器](https://github.com/nwtgck/piping-server)
*   静态托管
*   渐进式网络应用程序(PWA)
*   无账户

## 感谢

[@karanganesan](https://dev.to/karanganesan) 告诉我，简单一点的例子会有用。这就是我写这篇文章的原因。谢谢 Karan！

> 你能分享一些更简单的使用管道服务器的例子吗？比如:只有纯普通的 javascript 和最低限度的使用管道工作聊天，没有任何 Vue JS 或任何东西。