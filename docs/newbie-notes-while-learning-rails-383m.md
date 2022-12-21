# 新手学习 rails 时的笔记

> 原文：<https://dev.to/viricruz/newbie-notes-while-learning-rails-383m>

这是一本小日记...📓
如果你使用 heroku 部署你的应用和数据库，不要忘记在`git push heroku master`之后运行`heroku run rails db:migrate`。

您可以使用多个 rails 版本，这在您按照以前版本而不是当前版本的教程进行操作时非常有用。只是不要忘记**下划线**而`rails *5.1.6* new app_title`5 . 1 . 6 可以是另一个版本。

如果你修改了你的 **Gemfile** ，不要忘记在修改后`bundle install`。

总是检查在代码的某个地方，冒号**是在变量`name:`的前面还是在变量`:name`的后面，这总是让我在学习 rails 的时候遇到麻烦。**

这是我现在学习时发生的事情，如果你想分享你在学习 rails 时的经验，请添加评论😋