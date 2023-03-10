# 关于这一新开发功能的反馈？

> 原文：<https://dev.to/devteam/feedback-on-this-new-dev-feature-5cai>

## <mark>🚨轮询不是 DEV/Forem 上的功能特性。这篇文章纯粹代表一个概念的证明。</mark>

* * *

### 这里是我今天早些时候写的一个帖子:

[![ben](img/fe64a787b888dfb20fc13ad1e466da3d.png)](/ben) [## 你周末编码吗？多久一次，你的日常生活是怎样的？

### 本哈尔彭 6 月 15 日 191 分钟阅读

#discuss](/ben/do-you-code-on-the-weekend-how-often-what-s-your-routine-like-4h00)

### 在谈论我这个周末都在做些什么之前:

[![ben](img/fe64a787b888dfb20fc13ad1e466da3d.png)](/ben) [## 在工作中只写代码是非常好的，不要让任何人告诉你其他的事情。

### 本哈尔彭 2 月 17 日 181 分钟阅读

#career #productivity](/ben/its-perfectly-fine-to-only-code-at-work-dont-let-anyone-tell-you-otherwise--25i3)

### 我的另一个回答:

我一直在利用这个周末来发布一些想法，这些想法对于我或团队的其他成员来说还没有进入工作日优先箱。这份工作中我最喜欢的部分就是在压力很小的时候做我想做的事情。

我刚刚推送了一个这样的功能，希望得到一些反馈:

# **民意调查！**

### 民意测验对 DEV 来说是个好主意吗？

*   <input type="radio" id="poll_option_1" name="option" value="poll_option_1" data-option-id="1"> <label for="d" id="poll_option_label_1" data-option-id="1">是</label>
*   <input type="radio" id="poll_option_2" name="option" value="poll_option_2" data-option-id="2"> <label for="d" id="poll_option_label_2" data-option-id="2">否</label>
*   <button>只给我看结果</button>

在你兴奋之前，这个功能目前只对管理员有效，即使是管理员，你也不能在控制台之外创建投票。

我坚持保持投票和调查的有用性和准确性，即使这些不一定用于科学用途。因此，我认为我们应该共同制定出好的规则，来决定包含民意调查的文章在发表后是否允许被编辑(可能不允许，对吗？)之类的东西。

我们也想拿出一个很好的界面，包括在职位投票。我想到了两种方法。

## 首先在单独的用户界面中创建投票，然后进入如下帖子:

```
{% poll e41 %} 
```

Enter fullscreen mode Exit fullscreen mode

## 定义完全内嵌的投票:

```
{% poll %}
How do you pronounce dev.to?
- dev dot tee oh
- dev dot too
- dev dot toe
- dev toodilydoo
- dev too
{% endpoll %} 
```

Enter fullscreen mode Exit fullscreen mode

### 应该如何创建民意调查？

*   <input type="radio" id="poll_option_3" name="option" value="poll_option_3" data-option-id="3"> <label for="d" id="poll_option_label_3" data-option-id="3">顺路(`{% poll e41 %}` )</label>
*   <input type="radio" id="poll_option_4" name="option" value="poll_option_4" data-option-id="4"> <label for="d" id="poll_option_label_4" data-option-id="4">【行内(`{% poll %} ... {% endpoll %}` )</label>
*   <button>只给我看结果</button>

投票功能应该是可扩展的，用于创建调查和其他整洁的东西。我认为如果我们做得好的话，这将会很有趣并且对这个行业非常有用。我还认为，如果我们创建一些民意调查趋势的汇总数据分析会很酷，因为人们可以匿名使用该功能并进行分析。这可能是一种帮助人们对他们构建的软件做出更好选择的灵活方式。在此之前，我们希望非常清楚数据将如何使用，并提供简单的选择退出选项，将您的投票纳入这种聚合中。

我不确定这个什么时候会被普遍使用，但是你可以随时进入我们的回购程序并改进这里的任何东西。由于这主要是一个周末功能，我发布它是一个惊喜，所以开发团队的其他人可能会通过这个帖子了解它。😄

投票目前接受多达 15 个选项，并允许使用一些内嵌的 markdown 标签，如`code`元素。选项计数是相当随意的，但也许我们可以在封闭测试中敲定所有的小细节。多选项投票似乎也是一个好主意。我给模式留了足够的灵活性来构建这个想法，但是没有在这次推送中实现它。

这里有一个民意调查，可以包含在上述关于周末编码的帖子中:

### 你周末多久写一次代码？

*   <input type="radio" id="poll_option_7" name="option" value="poll_option_7" data-option-id="7"> <label for="d" id="poll_option_label_7" data-option-id="7">有些周末</label>
*   <input type="radio" id="poll_option_5" name="option" value="poll_option_5" data-option-id="5"> <label for="d" id="poll_option_label_5" data-option-id="5">每个周末</label>
*   <input type="radio" id="poll_option_8" name="option" value="poll_option_8" data-option-id="8"> <label for="d" id="poll_option_label_8" data-option-id="8">很少</label>
*   <input type="radio" id="poll_option_6" name="option" value="poll_option_6" data-option-id="6"> <label for="d" id="poll_option_label_6" data-option-id="6">大多数周末</label>
*   <input type="radio" id="poll_option_9" name="option" value="poll_option_9" data-option-id="9"> <label for="d" id="poll_option_label_9" data-option-id="9">从不</label>
*   <button>只给我看结果</button>

## P.S

说到新事物...如果你查看帖子“反应”区域的“三点”菜单，你可以看到新的通知设置，允许你订阅评论线程。这将有助于及时了解有趣的讨论、ama 等。视岗位而定。

我们认为人们会在这种使用模式中发现很多价值。在我们对这个特性做一个大的声明/推广之前，我们想发布一些额外的工具来在通知页面中提供更多的上下文。但既然你在读这个周末小公告，我觉得我也应该让你知道。

如果你跟进回购，你可能已经知道所有这些东西了。

编码快乐！