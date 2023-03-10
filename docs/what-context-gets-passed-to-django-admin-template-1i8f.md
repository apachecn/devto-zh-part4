# 传递给 Django 管理模板的上下文是什么

> 原文：<https://dev.to/connorbode/what-context-gets-passed-to-django-admin-template-1i8f>

所以我在修改 Django Admin，比如 **heavy** 。

很好玩。我真的很喜欢这个平台。你可以造出一个仪表盘，像**闪电般的速度**。

不管怎样，闲聊够了。

假设您正在编辑`change_form.html`模板。(你知道，它允许你改变一个模型的实例)，并且你想知道什么样的上下文变量被传递给那个模板。

如果你和我一样，你会去 Django 的 Github repo 获取[默认`change_form.html`](https://github.com/django/django/blob/master/django/contrib/admin/templates/admin/change_form.html) 的内容。

然后，您想知道什么上下文被传递给模板。你应该这么做:

1.  在模板中找到一个变量。我刚刚发现了`is_popup`。
2.  使用 Github 的搜索栏在 Django repo: ![](img/3c0d999f4ac965416ead4d8c26a1b0d6.png)中查找`is_popup`的实例
3.  请注意，第一个结果中有单词`context`出现..可能是个好赌。![](img/6324be5b32890be82db2b78c79c1c570.png)
4.  点击结果，您将看到[视图构建的完整上下文](https://github.com/django/django/blob/b9cf764be62e77b4777b3a75ec256f6209a57671/django/contrib/admin/templatetags/admin_modify.py#L65)

✌️:这就是现在，希望这个提示能帮助你！

* * *

我可以在 Twitter @connorbode 或者在我写代码的 [matix.io 找到](https://matix.io)[。](https://twitter.com/connorbode)

过来打个招呼👋！