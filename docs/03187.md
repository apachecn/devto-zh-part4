# 扩展 NEX mo–流式转录比较

> 原文：<https://dev.to/vonagedev/extending-nexmo-streaming-transcription-comparison-hb1>

在交流的世界里，词语是至关重要的，而获得正确的词语是极其重要的。语音通话记录可以帮助企业了解销售内容的新兴趋势，并为电话通话提供支持。

转录是将音频对话转换成书面文字的过程。在过去，这是由人类听录音并手动键入单词来处理的。这种做法今天仍然存在。现在出现了自动化服务，允许软件开发人员向服务发送音频文件，以获得更直接的结果。

这项工作本身就可以提供令人难以置信的见解，但我特别关注流转录，其中实时信息可以为您的销售和支持团队提供另一个层面的细节。

## 概述

这篇文章是对四家领先的云提供商的流媒体转录服务的比较和概述:[亚马逊转录](https://aws.amazon.com/transcribe/)、 [Azure 认知语音服务](https://azure.microsoft.com/en-us/services/cognitive-services/speech-to-text/)、[谷歌云语音转文本](https://cloud.google.com/speech-to-text/)和 [IBM 沃森语音转文本](https://www.ibm.com/watson/services/speech-to-text/)。

使用 Nexmo 语音 API 和 WebSockets，我用手机连接了一个语音电话，并传输了音频来测试这项服务。在这个过程中，我做了一些关于功能、易用性、准确性和成本的笔记。

## 准确度

选择转录服务时，一个更重要的因素是其准确性。拥有不正确的信息会导致任何数据不可信。

我用手机测试了每项服务的采样率。你也可以用`16kHz`，但在某些情况下，它实际上不太准确。我使用不同的短语，以相对平均的语速和适中的音量说话。房间也足够安静，可以消除背景噪音。

每项服务都毫无问题地转录了我使用的短语，但其中一些取决于我说得有多好。不幸的是，任何服务都缺乏清晰的表达。

否则，你可以对这些服务中的任何一个来处理你的转录感到相当有信心。

### “站在门外。”

*   IBM Watson 语音转文本——90 %(无论我说得多清楚，它都无法“站”起来)
*   Azure 认知语音服务–100%
*   谷歌云语音转文本–100%
*   亚马逊转录–100%

### 【食用前摇匀】

*   IBM Watson 语音转文本–100%
*   Azure 认知语音服务–100%
*   谷歌云语音转文本–100%
*   亚马逊转录–100%

### “彼得·派珀摘了一佩克腌辣椒。”

*   IBM Watson 语音转文本–100%
*   Azure 认知语音服务–100%
*   谷歌云语音转文本–100%
*   亚马逊转录–100%

## 特性

大多数服务的功能集非常相似:

*   使用机器学习来提高准确性
*   用于优化的定制模型
*   音频文件和流媒体
*   专有名词的上下文格式
*   标点支持

### 语言支持

[谷歌云语音转文本](https://cloud.google.com/speech-to-text/docs/languages)拥有最多的 120 种支持语言，在大多数情况下可以自动检测语言。紧随其后的语言支持是 [Azure 认知语音服务](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/language-support)，有 30 个用户， [IBM Watson 语音转文本](https://cloud.ibm.com/docs/services/speech-to-text?topic=speech-to-text-models#modelsList)，有 15 个用户。[Amazon transcripte](https://docs.aws.amazon.com/transcribe/latest/dg/what-is-transcribe.html)最受限制，只有 5 种语言可用于流式转录(更多语言可用于音频文件)。

## 易用性

每个供应商都有自己独特的成功之路。它们都提供了各种格式的入门指南。然而，它们往往要么用于发送音频文件，要么用于捕获麦克风输入。虽然麦克风输入是我们需要与 Nexmo WebSocket 一起使用的流方法，但它附带了一些不必要的额外前端代码。

### 谷歌云语音转文字

Google SDK 很容易实现，只需用提供的 SDK 和服务帐户用户期间提供的 Google 凭证文件创建一个`SpeechClient`。

`SpeechClient`提供了一个名为`streamingRecognize`的方法，该方法提供事件。

```
const client = new speech.SpeechClient(); let request ={ config: { encoding: 'LINEAR16', sampleRateHertz: 8000, languageCode: 'en-US' }, interimResults: false }; const recognizeStream = client .streamingRecognize(request) .on('error', console.error) .on('data', data => { console.dir(data, {depth: null}); }); 
```

谷歌的简单性比其他竞争对手更有优势。您可以不费吹灰之力就启动并运行，对我来说，这是一个巨大的胜利。

[谷歌云语音转文本文档](https://cloud.google.com/speech-to-text/docs/)

### Azure/IBM

IBM 和 Azure 都提供了特定于语言的 SDK 和大量入门文档，但是 API 直接调用实现起来很简单。这些服务都可以通过使用`wss://`协议连接到一个 API 路由并提供一个密钥来使用。

#### Azure 认知语音服务

```
wss://region.stt.speech.microsoft.com/speech/recognition/interactive/cognitiveservices/v1?format=simple&language=LANG\_CODE 
```

#### IBM 沃森语音转文本

```
wss://stream.watsonplatform.net/speech-to-text/api/v1/recognize?model=langage\_model 
```

还提供了一些额外的标题，但是您会发现每个服务的文档都是一个有益的资源。

*   [IBM Watson 语音转文本文档](https://cloud.ibm.com/apidocs/speech-to-text)
*   [Azure 认知语音服务文档](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/)

#### 亚马逊转录

我必须承认，AWS 对我来说是最难实现的。文档中的例子很少，而且 SDK 似乎根本不支持流媒体服务。当我第一次着手构建 Amazon 转录集成时，HTTP/2 是唯一可用的协议，并且要求对发送到服务器的每个请求进行签名。作为一个 AWS 新手，这对我来说相当棘手。

这个故事发生了一个巨大的转折，当开发者关系部的负责人 [Brandon West](https://twitter.com/bwest) 介入，用 WebSocket 连接写了一些样本代码，救了我。样本代码使事情变得更容易理解，但是一般的授权过程和呼叫签名使得 Amazon Transcribe 总体上更难实现。

这里我使用了`WebSocket`节点包并创建了一个签名的 URL 来创建连接。

```
let url = v4.createPresignedURL( 'GET', `transcribestreaming.${process.env.AWS_REGION}.amazonaws.com:8443`, '/stream-transcription-websocket', 'transcribe', crypto.createHash('sha256').update('', 'utf8').digest('hex'), { 'key': process.env.AWS\_ACCESS\_KEY\_ID, 'secret': process.env.AWS\_SECRET\_ACCESS\_KEY, 'protocol': 'wss', 'expires': 15, 'region': process.env.AWS\_REGION, 'query': `language-code=${process.env.LANG_CODE}&media-encoding=pcm&sample-rate=${process.env.SAMPLE_RATE}` } ); let socket = new WebSocket(url); 
```

亚马逊转录是一项优秀的服务，但如果它不是你习惯的东西，学习曲线可能是一个挑战。

[亚马逊转录文档](https://docs.aws.amazon.com/transcribe/latest/dg/what-is-transcribe.html)

## 成本

所有的服务提供商都提供一个 lite 或免费层来帮助您入门。基本成本是基于转录的音频时间量。提供商使用不同的时间增量，因此我在下表中对它们进行了标准化，以帮助您准确理解成本差异。

| 供应者 | 自由层 | 费用 | 标准化成本 |
| --- | --- | --- | --- |
| [亚马逊转录](https://aws.amazon.com/transcribe/pricing/) | 12 个月每月 60 分钟 | 0.006 美元/10 秒左右 | 0.036 美元/分钟 |
| [Azure 认知语音服务](https://azure.microsoft.com/en-us/pricing/details/cognitive-services/speech-services/) | 每月 5 小时免费音频 | 1 美元/音频小时 | 0.016 美元/分钟 |
| [谷歌云语音转文本](https://cloud.google.com/speech-to-text/pricing) | 60 分钟免费 | 0.006 美元/15 秒 | 0.024 美元/分钟 |
| [IBM Watson 语音转文本](https://www.ibm.com/cloud/watson-speech-to-text/pricing) | 每月 500 分钟 | 0.02 美元/分钟 | 0.02 美元/分钟 |

## 重述

流式转录是一种提供实时洞察力的好方法。四大领先的云提供商都提供可靠、准确的语音转文本服务。使用 Nexmo 作为音频流是一个很好的扩展，可以为您的客户带来更深入的交流体验。

我强烈推荐谷歌语音转文本作为一个可靠的选择。价格有竞争力，服务强大可靠。很容易设置并立即开始使用，这是一个额外的好处。

如果你想尝试任何或所有这些服务，Nexmo Extend 团队已经创建了示例代码来帮助你开始。

*   [亚马逊转录](https://github.com/nexmo-community/voice-aws-transcribe)
*   [Azure 认知语音服务](https://github.com/nexmo-community/voice-microsoft-speechtotext)
*   [谷歌云语音转文本](https://github.com/nexmo-community/voice-google-speechtotext)
*   [IBM Watson 语音转文本](https://github.com/nexmo-community/voice-watson-speechtotext)

帖子[扩展 NEX mo–流转录比较](https://www.nexmo.com/blog/2019/09/10/extending-nexmo-streaming-transcription-dr)最先出现在 [Nexmo 开发者博客](https://www.nexmo.com)上。