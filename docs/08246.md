# 脸书官方 API +聊天机器人第 1 部分:设置环境

> 原文：<https://dev.to/takunda/facebook-unoficial-api-chatterbot-part-1-setting-environment-4boc>

大家好，在这个系列中，我将谈论如何创建一个 facebook 机器人+聊天机器人。我不想在这里说教。我就开门见山了。

### FBChat

根据 pypi **的说法，fbchat** 是一个用于 Python 的脸书聊天(信使)库。不需要使用 XMPP 或 API 密钥。只需使用您的电子邮件和密码，您就可以开始了。换句话说，FBChat 是非官方的，不要做任何可疑的事情，否则你的帐户将被封锁。

### 话匣子

ChatterBot 是一个基于机器学习的对话引擎，内置于
Python 中，它使得基于
已知对话的集合生成响应成为可能。ChatterBot 的语言独立设计允许它
被训练说任何语言。这意味着你可以用中文，肖纳语，印度教等等

### 先做第一件事

#### 创建一个 python 环境

`virtualenv -p python3 facebookapp`

#### 激活环境

`source facebookapp/bin/activate`

#### 现在安装库

`pip install fbchat`
T1】

库安装完毕，我们现在可以进入下一部分了。

拜拜