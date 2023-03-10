# 使用 webRTC 和 Node.js 构建 P2P 视频聊天应用

> 原文：<https://dev.to/ganeshmani/building-p2p-video-chat-application-using-webrtc-and-node-js-lm0>

在本文中，我们将了解如何使用 webRTC 和 Node.js 构建对等视频聊天应用程序

关于 Node.js 的更多文章

[面向 Node.js 开发者的 Apache Kafka】](https://cloudnweb.dev/2019/08/apache-kafka-for-node-js-developers/)

[在 Node.js 中实现 Redis 发布/订阅](https://cloudnweb.dev/2019/08/implementing-redis-pub-sub-in-node-js-application/)

完整的源代码可以在[这里](https://github.com/ganeshmani/node-p2p)找到

### 什么是对等网络？

对等网络是客户端充当服务器的网络。对等网络中没有中央服务器。

[![](img/ff6bacafe4160999f8960a0c8d49f854.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--w6VEIzNt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cloudnweb.dev/wp-content/uploads/2019/09/nodep2p2.png)

了解有关[点对点应用](https://www.youtube.com/watch?v=oCS05QSQ-1k)的更多信息

### 构建对等应用程序

由于我们在对等应用中不需要服务器，您可能会想 Node.js 在这里的作用是什么。我们在 node.js 安润中构建了一个在浏览器中的对等应用程序。

首先，在 Node.js 中很少有 npm 包来帮助构建对等应用程序。

*   信号中枢
*   [webRTC-swarm](https://github.com/mafintosh/webrtc-swarm)
*   简单对等

主要是将对等体与另一个对等体连接起来。我们需要告诉其他对等体与我们的对等体连接。为了解决这个问题，我们正在使用 **SignalHub** 。

SignalHub 将消息发送给另一个对等体进行连接。webRTC-swarm 通过 SignalHub 连接。简单对等使浏览器成为对等节点。

让我们使用 webRTc Swarm，simple-peer 构建一个视频聊天应用程序。

创建一个 videoplayer.js 文件，并添加以下代码

```
module.exports = Player

function Player (data) {
  data = data || {}
  this.color = data.color || randomColor()
  this.x = data.x;
  this.y = data.y;
  this.top = data.top;
  this.left = data.left;
  this.name = data.name;
  this.element = document.createElement('video')
  Object.assign(this.element.style, {
    width: '40%',
    height: '50%',
    position: 'absolute',
    top: data.top+'px',
    left: data.left+'px',
    backgroundColor: this.color
  })
  document.body.appendChild(this.element)
}

Player.prototype.addStream = function (stream) {
  this.element.srcObject = stream
  this.element.play()
}

Player.prototype.update = function (data) {
  data = data || {}
  this.x = data.x || this.x
  this.y = data.y || this.y
  Object.assign(this.element.style, {
    top: this.y + 'px',
    left: this.x + 'px'
  })
}

function randomColor () {
  return '#' + Math.random().toString(16).substr(-6)
}
```

视频播放器在浏览器中创建一个视频元素。我们使用 webRTC 通信来传输和操作 DOM 元素。

创建一个 index.js 文件，并添加以下代码

```
 navigator.mediaDevices.getUserMedia({ video: true, audio: true }).then(function (stream) {
    //This is used for Signaling the Peer    
    const signalhub = require('signalhub')
    const createSwarm = require('webrtc-swarm')
    //Creates the Signal rub running in the mentioned port
    const hub = signalhub('my-game', [
      'http://localhost:8080'
    ])
    const swarm = createSwarm(hub, {
      stream: stream
    })
    //Creates a video player
    const Player = require('./videoplayer.js')
    const you = new Player({ x: 0, y : 0 ,color : 'black',left : 0,top : 0})
    you.addStream(stream)

    const players = {}
    swarm.on('connect', function (peer, id) {
      if (!players[id]) {
        players[id] = new Player({
            x : 300,
            y : 0,
            left : 200,
            top : 0,
            color : 'red'
        })
        peer.on('data', function (data) {
          data = JSON.parse(data.toString())
          players[id].update(data)
        })
        players[id].addStream(peer.stream)
      }
    })
    //On webRTC Disconnets
    swarm.on('disconnect', function (peer, id) {
      if (players[id]) {
        players[id].element.parentNode.removeChild(players[id].element)
        delete players[id]
      }
    })

    setInterval(function () {
        console.log("Interval Call");
      you.update()

      const youString = JSON.stringify(you)
      swarm.peers.forEach(function (peer) {
        peer.send(youString)
      })
    }, 100)
  })
```

首先，我们创建一个 webRTC swarm，并用 SignalHub 映射它。

```
swarm.on('connect',function(peer,id){
     peer.on('data',function(data){
         //On receiving the data from peers, do some actions
    });
});
```

之后，swarm listener 监听连接。当其他对等体连接时，我们开始监听对等体发送的数据。

```
 swarm.peers.forEach(function (peer) {
        peer.send("Data to Send")
})
```

在 webRTC swarm 中，我们找到我们的对等节点，并将数据发送到所有其他节点。

#### 点对点视频流

主要是，流视频。我们需要让媒体从 web API 访问。您可以使用下面的代码来实现这一点。

```
navigator.mediaDevices.getUserMedia({ video: true, audio: true }).then(function (stream) {
    //Gets Media Access
});
```

之后，您需要将流添加到 webRTC swarm，以访问对等网络中的数据。

```
 const swarm = createSwarm(hub, {
      stream: stream
    })
```

最后，您需要将流添加到视频元素中。

```
Player.prototype.addStream = function (stream) {
  this.element.srcObject = stream
  this.element.play()
}
```

#### 运行应用程序

要运行应用程序，您需要首先运行 SignalHub 并运行服务器。

```
$ npm run signalhub
$ npm run start
```

#### 演示

使用 webRTC 和 Node.js 构建 P2P 视频聊天应用

[![](img/9956939bb9cf2a208dfb3d62e480d3c7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eyCm4gAT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cloudnweb.dev/wp-content/uploads/2019/09/Screenshot-2019-09-01-at-1.43.48-PM-1024x566.png)

本文引用自[这段](https://www.youtube.com/watch?v=IqPJb6o_S1Q)视频