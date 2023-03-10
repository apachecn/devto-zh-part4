# 用 WebRTC 流式传输远程(Linux)屏幕

> 原文：<https://dev.to/rviscarra/stream-a-remote-linux-screen-with-webrtc-2m5m>

最近我参与了一个项目，涉及到一些服务器端的 GPU 渲染，
由于我们所使用的技术的性质，我们需要在我们的机器上运行一个 X 服务器。最初的设置相对容易，直到一个奇怪的错误开始
出现在一些服务器上，并且日志没有提供足够的信息来
有效地调试，这就是我如何结束编码一个远程查看器，使用 WebRTC 流式传输
一个远程服务器“屏幕”。

TL；DR:完整的源代码可以在这个回购上找到。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[rviscarra](https://github.com/rviscarra)/[webrtc-远程屏幕](https://github.com/rviscarra/webrtc-remote-screen)

### 将远程桌面屏幕直接传输到您的浏览器

<article class="markdown-body entry-content container-lg" itemprop="text">

## WebRTC 远程视图

### 属国

*   [Go 1.12](https://golang.org/doc/install)
*   如果您想要 h264 支持:libx264(包含在 x264-go 中，您将需要一个 C 编译器/汇编器来构建它)
*   如果你想要 VP8 支持:libvpx

### 体系结构

[![Architecture](img/15679ef9ffea0a21a4313b433f076f33.png)](https://raw.githubusercontent.com/rviscarra/webrtc-remote-screen/master/docs/architecture.png)

更多信息请见[这篇博客文章](https://viscarra.dev/post/webrtc-remote-screen/)。

### 运行服务器

服务器通过命令行接收以下标志:

`--http.port` (Optional)

指定 HTTP 服务器应该监听的端口，默认情况下使用端口 9000。

`--stun.server` (Optional)

允许指定不同的 [STUN](https://es.wikipedia.org/wiki/STUN) 服务器，默认使用谷歌 STUN 服务器。

支持 Chrome 74+，Firefox 66+，Safari 12.x。旧版本(在合理范围内)也应该得到支持，但 YMMV。

### 构建服务器

通过运行`make`构建*部署*包。这应该创建一个带有二进制和 web 目录的 tar 文件，默认情况下只支持 h264，如果你想使用 VP8 运行`make encoders=vp8`，如果你想…

</article>

[View on GitHub](https://github.com/rviscarra/webrtc-remote-screen)

### 好吧，但是为什么呢？

我知道这个问题的其他解决方案，比如设置[虚拟 GL](https://wiki.archlinux.org/index.php/VirtualGL#Using_VirtualGL_with_VNC)
通过 VNC 传输远程 X 服务器的视频。我不喜欢这个解决方案
,因为:

1.  安装起来不像复制一个 zip 文件，解压并运行它那么简单。
2.  必须事先在每个实例上安装并启动它。
3.  这将是另一个需要维护的组件，它有自己的未知和问题。

### 总体概述

[![Architecture](img/ff247ed040eaa0de8f35ce4b096822ba.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0zPMshyU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/is7as67r1wzag0t9ganr.png)

以下是该工具的基本要点:

1.  默认情况下，该服务在端口 9000 上启动并侦听，这可以通过标记进行更改。
2.  该服务公开了两个端点，`POST /session`用于启动会话，`GET /screens`用于从远程服务器获取可用屏幕。
3.  一旦选择了一个屏幕，就会在浏览器中创建一个 SDP 报价，为了简单起见，这里有一个变通办法来避免滴冰。
4.  一旦收集了所有 ICE 候选项，就会发出一个带有所选屏幕和浏览器 SDP 的`POST /session`请求。
5.  后端检查该提议，检查浏览器是否支持 h264 baseline 3.1，如果一切顺利，服务器用 SDP 应答进行响应。
6.  现在，浏览器应该接收并显示视频流。

### 实现细节

#### h264

我决定用 h264 而不是 VP8，因为 Golang 对 h264 的绑定感觉比 VP8 更习惯，也更好。这也意味着我们可以支持旧版本的 Safari 。

#### Sendonly 支持

因为这个应用程序只需要将媒体流式传输到浏览器，所以我需要在服务器上支持 sendonly 收发器。在阅读了皮恩的代码后，我意识到这在当时是不可能的。然而，并不是一切都那么糟糕，对这一特性的需求促使我实现了它，并为π介子回购做出了贡献！

#### X 服务器(仅？)支持

我用来截图的库支持多种平台，所以在
*理论*中它应该可以工作，但是我没有测试过，如果你做 YMMV 的话。唯一的*硬*T5】依赖是 libx264(参见上面的 h246 部分)。

#### 客户端代码

我想让事情变得简单，所以我没有设置 Webpack/Babel 来转换到
ES5 或者把我的应用程序分成多个文件。这样做的主要原因是
我想使*部署*尽可能简单，压缩它，压缩它，解压缩
并运行！

### 火狐 WebRTC bug 的诅咒

我原本打算在几周前发布这个回购/博客帖子，因为该项目当时是一个很好的 MVP。然而，生活并非如此简单...就在我准备回购的自述文件并试图在 FF 中截屏时，我意识到我没有工作，这让我觉得很奇怪，因为我之前让它工作过。

原来，我碰到了这个奇怪的相对老的 bug，当 Firefox 是提供者时，它让 Firefox 显示一个“黑”视频，而不是输入流。调试和修复花了一些时间，因为基本上没有错误的痕迹(作为一个*经验丰富的*开发者，我自动假设这个错误是我的错)。

幸运的是这并不难修复，我最终做了这样的事情:

```
func findBestCodec(sdp *sdp.SessionDescription, profile string) (*webrtc.RTPCodec, error) {
    for _, md := range sdp.MediaDescriptions {
        for _, format := range md.MediaName.Formats {
            intPt, err := strconv.Atoi(format)
            payloadType := uint8(intPt)
            sdpCodec, err := sdp.GetCodecForPayloadType(payloadType)
            if err != nil {
                return nil, fmt.Errorf("Can't find codec for %d", payloadType)
            }

            if sdpCodec.Name == webrtc.H264 {
                packetSupport := strings.Contains(sdpCodec.Fmtp, "packetization-mode=1")
                supportsProfile := strings.Contains(sdpCodec.Fmtp, fmt.Sprintf("profile-level-id=%s", profile))
                if packetSupport && supportsProfile {
                    var codec = webrtc.NewRTPH264Codec(payloadType, sdpCodec.ClockRate)
                    codec.SDPFmtpLine = sdpCodec.Fmtp
                    return codec, nil
                }
            }
        }
    }
    return nil, fmt.Errorf("Couldn't find a matching codec")
} 
```

它主要根据编解码器名称和配置文件寻找我支持的编解码器，在本例中为
H264 受限基线 3.1 ( `42e01f`)。

### 看起来怎么样？

这就是，在火狐上工作

[![Screenshot](img/707afd0adefc21d5e9d77234923137fe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Y0jTOJ_d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1w5gfs3taycytow2arkq.png)

[这里是它的一个简短截图](https://gfycat.com/ifr/ThunderousBaggyJunebug)

### 你认为那有用/有趣吗？

我目前正在加拿大或欧洲寻找一份软件工程的工作
(现场)，知道谁可能感兴趣吗？[通知我！](https://viscarra.dev/about)