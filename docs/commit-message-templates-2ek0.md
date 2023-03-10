# 提交消息模板

> 原文：<https://dev.to/hartmann/commit-message-templates-2ek0>

这里有一个给你的声明:如果你和别人一起做一个项目，你*应该*使用提交消息模板。它不仅有助于提高你的信息的一致性和有用性，而且，也许更重要的是，它将减少写信息所需的认知负荷和时间。我只能代表我自己，但是我知道必须去查找关于提交消息应该如何构造以及它引用什么的信息会导致一些不必要的上下文切换，导致工作流效率降低。

## 设置

设置提交消息模板只需要几个步骤:

1.  创建一个模板。

    您可以使用任何文本文件作为模板；只需将您想要显示在提交消息缓冲区中的内容放在那里。文本的普通行将显示为内容，而以注释字符(`#`默认)开始的行显示为注释。

2.  将其作为提交模板添加到 git 配置中:`git config commit.template path/to/file`。

3.  搞定了。现在试着做点什么。

* * *

这样做的时候，你是在本地分配模板(给当前的回购)。如果您想在全局范围内完成，请传递`--global`选项。

如果您想在某个子目录中的多个项目之间共享模板，您可以通过将模板与 git 的条件包含功能相结合来实现。但是要注意，给定一个文件的相对路径，git 将查找相对于包含配置的位置的文件。虽然这有可能被利用来为不同的项目使用不同的模板，但到目前为止我还没有发现它的必要性，并且坚持使用具有绝对路径的单个模板。

如果您想取消模板设置，请使用`git config --unset`功能:

```
git config --unset commit.template 
```

## 提交模板意见

对于提交消息，不同的团队和项目有不同的需求，所以没有“一刀切”，但这里有一些基于我所在团队的消息格式的想法。

### 标签列表

如果你用包含的工作类型来标记你的提交，并且有一个定义好的标签集(例如，“特性”，“bug”，“琐事”，“重构”等等)。)，在模板中列出所有不同的选项可能是一个好主意，这样您就可以在提交代码时查看它们。用注释行列出它们，你甚至不用删除它们；它们只会在提交缓冲区中显示为额外的信息。

虽然团队中有经验的成员可能会记住这些，但是对于一个新人来说，将列表放在提交缓冲区中是非常有帮助的，这样他们就不必去查找它们了。

### 问题识别

如果标准要求在提交消息中列出一个问题/票据 ID，您可以在模板中放置一个小占位符，告诉您用相关的 ID 替换它。

如果您还处理特性分支，并根据它们所对应的特性/问题 ID 来命名它们，您甚至可以让 git 使用钩子自动填充这个问题编号，正如我在上一篇文章中解释的那样。我发现这为我节省了惊人的大量时间和精神开销。

### 非常具体的格式

当你的信息需要匹配一个非常特殊的格式，并且很难准确地记住什么该放在哪里时，模板是一个完美的解决方案。

例如，如果你正在处理上面两个的组合，你想要标签*和 ID*，并且你想要它们以特定的顺序排列，甚至可能有一个特定的分隔符(*看起来很麻烦，但是使用它*)，你可以创建一个模板，你可以只填写相关的部分，省去了每次都要查找格式的麻烦。