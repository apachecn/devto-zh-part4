# 代码烘烤器:WebRTC

> 原文：<https://dev.to/hoffmann/code-roaster-webrtc-6a9>

# 简介

我想从其他人的贡献开始一个小系列，叫做“代码烘烤器”。这是一个邀请分享你的代码建设性的批评。

当别人给我看别人的代码时，我听到最多的一句话是

> 我知道这完全是一团乱麻，但它在起作用，我会在未来的某个时候以正确的方式去做。

让我们这次努力做好这件事，打开我们的锅盖，看看其他人的工作。尝一尝，加入你的一粒盐。

# 关于我和我的工作

Hy，我是一名德国大学的全栈开发人员，一直在努力提高我的编程技能，同时跟上当前 web 技术的发展。我在 IT 领域最大的斗争是对过时浏览器(如 IE10)及其技术积压的无休止的斗争。

# 今日项目

2013 年，Chris Ball 发表了一个无服务器的 WebRTC 示例，让用户通过 IM 等方式发送信号。代码被维护了三年，直到没有进一步的开发。我的目标是在普通 js 中使用现代 web 技术将示例最小化到最小的工作代码库。

我拿走了代码，扔掉了所有在我看来不必要的东西，并试图将 webrtc 逻辑与用户界面分开。因此，我丢掉了所有的设计，因为我真的不擅长这个(邀请任何想做 ui/ux 练习的人)。

# 设计决策

我将登录分成两个通过事件交互的类/模块。耦合似乎不必要的复杂:

```
 const webRtc = new WebRTC(stunServers)
    const gui = new GUI(webRtc)
    webRtc.weave(gui) 
```

也许有人知道如何打磨这个？除此之外，我找不到任何方法来改进我的代码。我邀请你放弃你的 2，取而代之的是更好的、更易读的、运行更快的、可重用的、可维护的、结构良好的代码。点燃烘焙炉！

# 代码

完整的项目可以在 [Github](https://github.com/HoffmannP/WebRTC) 找到

## GUI 模块

```
/* global EventTarget, CustomEvent */

function timestamp () {
  return (new Date()).toTimeString().substr(0, 8)
}

export default class GUI extends EventTarget {
  constructor (target) {
    super()
    this.target = target

    this.connect = document.querySelector('.connect')
    this.connect.querySelector('button').addEventListener('click', _ => this.clickConnect())
    this.descriptionBox = this.connect.querySelector('textarea')
    this.descriptionBox.value = ''

    this.chat = document.querySelector('.chat')
    this.chat.querySelector('button').addEventListener('click', _ => this.sendMessage())
    this.messageBox = this.chat.querySelector('input[type="text"]')
    this.chatBox = this.chat.querySelector('.chatlog')

    this.addEventListener('candidate', candidateEvent => (this.descriptionBox.value = JSON.stringify(candidateEvent.detail)))
    this.addEventListener('connect', connectEvent => {
      this.connect.style.display = 'none'
      this.chat.style.display = 'initial'
    })
    this.addEventListener('message', messageEvent => this._addChatLine(messageEvent.detail, 'you'))
  }

  trigger (name, detail) {
    this.target.dispatchEvent(new CustomEvent(name, { bubbles: true, detail: detail }))
  }

  clickConnect () {
    const description = this.descriptionBox.value
    try {
      this.trigger('connect', JSON.parse(description))
    } catch (e) {
      this.trigger('connect')
    }
    this.descriptionBox.value = ''
  }

  sendMessage () {
    const message = this.messageBox.value
    if (message.length) {
      this._addChatLine(message, 'me')
      this.trigger('message', message)
    }
    this.messageBox.value = ''
  }

  _addChatLine (text, type) {
    this.chatBox.insertAdjacentHTML('beforeend', `<p class="from-${type}">[${timestamp()}] ${text}</p>`)
    this.chatBox.scrollTop = this.chatBox.scrollHeight
  }
} 
```

## WebRTC 模块

```
/* global EventTarget, RTCPeerConnection, CustomEvent */

export default class webRTC extends EventTarget {
  constructor (iceServers) {
    super()
    this.connection = new RTCPeerConnection({ 'iceServers': iceServers })
    this.connection.addEventListener('icecandidate',
      ICECandidateEvent => ICECandidateEvent.candidate === null && this.trigger('candidate', this.connection.localDescription))
    this.connection.addEventListener('datachannel',
      ChannelEvent => this.connectChannel(ChannelEvent.channel))
    this.addEventListener('connect', this.connectHandler)
  }

  weave (target) {
    this.target = target
  }

  trigger (name, detail) {
    this.target.dispatchEvent(new CustomEvent(name, { bubbles: true, detail: detail }))
  }

  connectHandler (connectEvent) {
    switch (this.connection.signalingState) {
      case 'stable':
        if (connectEvent.detail === null) {
          this.setupChannel()
          this.connection.createOffer()
            .then(desc => this.connection.setLocalDescription(desc))
        } else {
          this.connection.setRemoteDescription(connectEvent.detail)
          this.connection.createAnswer()
            .then(desc => this.connection.setLocalDescription(desc))
        }
        break
      case 'have-local-offer':
        this.connection.setRemoteDescription(connectEvent.detail)
    }
  }

  setupChannel () {
    this.connectChannel(this.connection.createDataChannel('channel'))
  }

  connectChannel (channel) {
    channel.addEventListener('open', _ => this.trigger('connect'))
    channel.addEventListener('message', messageEvent => this.trigger('message', messageEvent.data))
    this.addEventListener('message', message => channel.send(message.detail))
  }
} 
```