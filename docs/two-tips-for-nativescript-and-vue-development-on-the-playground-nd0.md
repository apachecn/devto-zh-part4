# 在操场上开发 NativeScript 和 Vue 的两个技巧

> 原文：<https://dev.to/raymondcamden/two-tips-for-nativescript-and-vue-development-on-the-playground-nd0>

好了，伙计们，正如标题所说，这里有两个使用 [NativeScript Playground](https://play.nativescript.org) 时要记住的提示。一个会有点咆哮/生气(抱歉！)我希望将来有一天会真正拯救你。先把生气的那个解决掉吧！

### 保存，再保存，再保存

关于操场，我很快发现的一件事是，如果你没有正确保存，就有可能“丢失”你的项目。当我第一次遇到这个问题的时候，我在三月份的时候提交了一个关于这个问题的文件。不过最近我遇到了一个新版本，这真的，真的让我很生气。这个 bug 是这样工作的:

*   在*未*登录的情况下工作
*   保存项目
*   意识到你没有先登录(哎呀！)并登录
*   请注意，保存 UI 被禁用，因为您刚刚保存了它。
*   关闭选项卡

你猜怎么着？项目*已被*保存，但它与您的用户无关。你刚刚失去了你的项目。最简单的解决方案是确保你总是先登录。如果你忘记了，一定要修改项目的*内容*，重新启用保存界面，然后再次保存。我建议进入代码并添加这个:

```
// Hey Progress, fix this damn issue! 
```

Enter fullscreen mode Exit fullscreen mode

嘿，我说过我有点生气，对吧？明确一点，这并不妨碍我热爱操场。我用它写了一篇刚刚结束的文章，非常适合它。我只是希望他们能尽快解决这个问题。

### 错误和该死的错误

这真的让我抓狂了一段时间，我不能责怪任何人，除了我自己。我上面提到的文章关注的是 Vue、NativeScript 和导航。我想它出来的时候你会喜欢的。真的，它会改变你的生活。但是在工作的时候，我遇到了一个奇怪的问题。我的演示有两页。第一页通过[手动路由](https://nativescript-vue.org/en/docs/routing/manual-routing) API 链接到第二页。

这个 API 使用起来非常简单。但是当我点击开始导航时，什么也没有发生。我没有在任何地方得到一个错误，它只是没有…好导航。

当我在日志中注意到这一点时，我基本上被卡住了:

```
NativeScript-Vue has "Vue.config.silent" set to true, to see output logs set it to false. 
```

Enter fullscreen mode Exit fullscreen mode

我的第一个想法是，改变这一点没有帮助。我没有出错，只是它什么也没做。但是我想这不会有什么坏处，所以我取消了对这行代码的注释:

```
// Vue.config.silent = false; 
```

Enter fullscreen mode Exit fullscreen mode

然后……瞧:

```
[Pixel 3 XL]: [Vue warn]: Unknown custom element: <StackView> - did you register the component correctly? For recursive components, make sure to provide the "name" option. 
```

Enter fullscreen mode Exit fullscreen mode

`StackView`？`StackView`是什么鬼？哦，是的，是这样的:

```
<template>
    <Page class="page">
        <ActionBar :title="film.title" class="action-bar" />
        <StackView height="100%">
         <!-- stuff here -->
        </StackView>
    </Page>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

你猜怎么着？那应该是`StackLayout`。那么为什么我没有得到一个错误呢？老实说，我不知道。正如上面的消息所述，我可以定义自己的组件`StackView`，这是有效的。然而，导航失败的事实对我来说似乎不仅仅是一个警告。

也就是说，如果我以后遇到类似的奇怪错误，我会(希望)记得尝试更改日志记录值。我的“常规”错误显示得很好，所以我不会默认更改它，但我下次肯定会先尝试一下。

*标题图片由[亚伦·伯顿](https://unsplash.com/@aaronburden?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 Unsplash* 上拍摄