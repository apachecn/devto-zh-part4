# 如何在 Rails 中设置一个模型来引用相同的模型类型？

> 原文：<https://dev.to/michael/how-would-you-setup-a-model-in-rails-to-reference-the-same-model-type-24fi>

假设我有一个叫做“章节”的模型。我希望每一章都能参考下一章和上一章。你将如何着手建立这种关系？

所以我要找的是这样的东西:

```
c = Chapter.first
c.next_chapter 
```

Enter fullscreen mode Exit fullscreen mode

应该返回下一章的章节对象。