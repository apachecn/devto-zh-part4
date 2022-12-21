# TPP 主题 3:软件熵

> 原文：<https://dev.to/steadbytes/tpp-topic-3-software-entropy-217g>

> 这篇文章最初出现在[steadbytes.com](https://steadbytes.com/blog/the-pragmatic-programmer-20th/topic-3-challenges/)

> 见实用程序员 20 周年纪念版系列[第一帖](https://dev.to/steadbytes/the-pragmatic-programmer-20th-anniversary-edition-series-1e2l)介绍。

## 挑战 1

> 通过调查你的项目社区来帮助加强你的团队。选择两到三扇破碎的窗户，与你的同事讨论问题是什么，以及如何修复它们。

我将讨论最近工作中的一个例子——当然具体细节已经删除。我们当前的主要项目是主 API 的下一个版本(3)。因为这是一个新版本，我们可以做出突破性的改变，但是仍然需要相当大的向后兼容性。新的 API 必须在先前 API 版本中现有用户的域模型和较新的模型之间进行转换。以前的模型有许多用户类型，每种类型都有不同的权限。这里有几个例子(还有更多):

*   只读
*   标准
*   极好的

这些使用具有如下值的`user_type`字段来表示:

```
READONLY = "readonly"
STANDARD = "standard"
SUPER = "super" 
```

新的 [Django](https://www.djangoproject.com/) `User`模型也有一个映射到这个字段的`user_type`字段。然而，由于我不想深入的原因，新的`user_type` s 有一个`_user`后缀。使用[选项](https://docs.djangoproject.com/en/2.2/ref/models/fields/#choices)的实现遵循以下原则:

```
from django.db import models

class User(models.Model):

    # user_type values used in API V2
    READONLY = "readonly"
    STANDARD = "standard"
    SUPER = "super"

    # mapping V2 user_type to Django group name
    USER_TYPES = [
        (READONLY, "readonly_user"),
        (STANDARD, "standard_user"),
        (SUPER, "super_user"),
    ]

    user_type = models.CharField(choices=USER_TYPES) 
```

忽略了**这个例子中许多**遗漏的细节；代码有效。先前的`user_type`被映射到新的，新的`user_type`的值根据需要使用 Django `choices`进行约束。也是在很短的时间内实现的——很棒吧？嗯，有一些破碎的窗户:

*   V2 API `user_type`的字符串值是新`User`模型的类属性。这使得`User`模型的接口变得混乱，只有通过阅读代码和附带的注释才能知道它们的用途。
*   用于`user_type`字段中`choices`参数的`USER_TYPES`列表非常不干燥(潮湿？).

我在代码审查期间提出了这些问题，我们在接受拉取请求之前修复了损坏的窗口:

```
from enum import Enum

from django.db import models

class V2UserTypes(Enum):
    READONLY = "readonly"
    STANDARD = "standard"
    SUPER = "super"

class User(models.Model):

    user_type = models.CharField(
        choices=[
            (t, t.value + "_user") for t in V2UserTypes
        ]
    ) 
```

通过将版本 2 的字符串常量移出类并删除`USER_TYPES`列表(它的唯一目的是支持`user_type`字段的`choices`参数)，消除了`User`模型的接口混乱。字符串常量现在封装在一个`Enum`中。这有两个主要好处:

1.  很清楚`V2UserTypes`的值代表什么
2.  `Enum`确保唯一、恒定的值。帮助他们抵御 Python 提供的高水平动态主义的双刃剑。

引入了 list comprehension，在第 2 版用户输入的内容中添加了后缀`_user`,以保持代码简洁。

由于这是一个新项目，在进入代码库之前修复这个坏窗口**对于确保代码库保持高质量非常重要。**

## 挑战 2

> 你能说出窗户第一次被打破的时间吗？你的反应是什么？如果这是其他人的决定或者管理法令的结果，你能做什么呢？

挑战 1 是一个能够发现窗户第一次被打破的例子。在这种情况下，我的反应是在代码审查期间提出来；解释为什么我认为这是一扇破窗，它在未来可能引起的问题，并提供一些潜在的解决方案。这是其他人的决定的结果，但是我很幸运在一个鼓励代码审查和反馈/从其他人那里学习的团队中。

另一方面，管理法令会更难处理。当然，这取决于法令的具体内容，但我的一般做法是:

*   从法令中提取破碎的窗户
*   从以前的项目、开源、研究论文等引入类似破窗的地方收集证据
*   解释为什么破窗是一个问题，以及(使用证据)可能产生的后果
*   提供一个解决方案，很可能是类似于“我希望在 *x* 天内实施 *x* 解决方案”的内容。这额外的时间将节省 *y* 未来的问题数量<参考证据此处>