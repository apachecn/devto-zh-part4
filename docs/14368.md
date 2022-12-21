# 用 5 个习惯改善拉动式请求

> 原文：<https://dev.to/chantastic/improve-pull-requests-with-5-practices-1ke9>

## 1。用动词开始你的信息

探索 git 历史总是一件苦差事。通过清晰有力的信息，你可以让家务变得更简单。
从动词开始。
保持简短。

`Fix e2e login spec`
`Add inactive person styling`
`Remove presence validation from age on Person model`

* * *

## 2。用命令式风格写信息

```
- Fixed nav jitter + Fix nav jitter 
```

```
- Added CTAButton + Add CTAButton 
```

*在堆栈溢出时读取[‘git 提交消息时我应该使用过去时态还是现在时态’](https://stackoverflow.com/a/3580764)*

* * *

## 3。用`have/need/get`来描述你的公关

将你的描述分成三个逻辑部分:
过去(拥有)、现在(需要)和未来(得到)。

### 有

描述存在的东西。展示对代码、团队和创建它的约束的同理心。
不要在过去拉屎。

### 需要

描述人类的需求。
这就是客户获得的价值。
先不要跳入技术细节。
你可以从特雷罗/吉拉那里复制/粘贴这个。

### 得到

> 你不能总是得到你想要的

米克·贾格尔是对的。用专业术语描述你的解决方案。
承认自己的不足。
指出你没有足够信息做出更好解决方案的领域。

### 奖金:参考

链接有助于形成您的解决方案的支持页面、文档和口头说明。给予帮助过你的同事信任。
这让审核者深入了解您的流程，并提高您的可信度。

* * *

## 4。使用`--fixup`标志提交修复

一旦审查开始，像`fix`、`oops`、`shit...`这样的无用信息就开始蜂拥而至。
使用`--fixup`标志使这些提交更具描述性。

`git commit --fixup a1b2c4`

Github 有一个[确认压缩和合并](https://github.blog/2016-04-01-squash-your-commits/)选项，将所有`fixup!`提交压缩到它们引用的提交中。

*在 thoughtbot 的博客*上阅读[自动挤压 Git 提交的](https://thoughtbot.com/blog/autosquashing-git-commits)

* * *

## 5。检查你的自我

请记住，你是在提出“拉式请求”，而不是“拉式需求”。“拉”请求是对话的开始。
不要认为你一切都是对的。
虚心接受反馈。你可能会学到一些你不知道的东西。

玩得开心🥳
让我知道你学到了什么🙌

✅ [chantastic](https://twitter.com/chantastic)