# 介绍“火箭人”,一个帮助你用纯 Ruby 编写发布/订阅代码的宝石。不需要导轨！

> 原文：<https://dev.to/edisonywh/introducing-rocketman-a-gem-to-help-you-do-pub-sub-code-in-pure-ruby-no-rails-needed-2pgf>

在这里发布我的第二个 Ruby gem，介绍`Rocketman`！

## 描述

Rocketman 是在 Ruby 代码中引入发布-订阅机制的一颗宝石。

与所有发布-订阅机制一样，这极大地解耦了上游生产者和下游消费者，允许可伸缩性，并在您决定将发布-订阅转移到单独的服务时更容易重构。

Rocketman 也可以在没有 Rails 的情况下工作。

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png) [ edisonywh ](https://github.com/edisonywh) / [火箭人](https://github.com/edisonywh/rocketman)

### 🚀Rocketman 帮助在 Ruby 中构建基于事件/发布/订阅代码

<article class="markdown-body entry-content container-lg" itemprop="text">

# 火箭人

[![rocketman](img/ae1611055106c0018eaf03f9eb0e3f9e.png) ](https://raw.githubusercontent.com/edisonywh/rocketman/master/./rocketman.jpg) <sub> *是的，我知道图像上说的是星际争霸*</sub>

> *<g-emoji class="g-emoji" alias="notes" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f3b6.png">🎶</g-emoji>我认为这将是一个很长很长的时间，直到着陆带我再次找到<g-emoji class="g-emoji" alias="notes" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f3b6.png">🎶</g-emoji>*

Rocketman 是一块宝石，它在 Ruby 代码中引入了发布-订阅机制。

Rocketman 的主要目标不是取代 Redis PubSub/Kafka 这样合适的消息总线，而是成为一个垫脚石。你可以在下面阅读更多关于项目背后的[原理。](https://github.com/edisonywh/rocketman#why-use-rocketman-rather-than-a-proper-message-bus-eg-redis-pubsubkafka)

与所有发布-订阅机制一样，这极大地解耦了上游生产者和下游消费者，允许可伸缩性，并在您决定将发布-订阅转移到单独的服务时更容易重构。

Rocketman 也可以在没有 Rails 的情况下工作。

## 装置

将这一行添加到应用程序的 Gemfile 中:

```
gem 'rocketman'
```

Enter fullscreen mode Exit fullscreen mode

然后执行:

```
$ bundle 
```

或者自己安装为:

```
$ gem install rocketman 
```

## 使用

火箭人曝光了两个模块，`Rocketman::Producer`和`Rocketman::Consumer`。他们完全按照他们的…

</article>

[View on GitHub](https://github.com/edisonywh/rocketman)

## 用法

发射事件看起来就像这样简单:

```
emit :hello, payload: {"one" => 1, "two" => 2} 
```

Enter fullscreen mode Exit fullscreen mode

而监听事件看起来是这样的:

```
on_event :hello do |payload|
  puts payload
end 
```

Enter fullscreen mode Exit fullscreen mode

如果你对这个项目感兴趣，在`Usage`部分和`Roadmap`有更多的信息，否则，也可以在这里随意评论！