# rails 中代码质量和格式的 robocop

> 原文：<https://dev.to/n350071/rubocop-for-code-quality-and-formatting-in-rails-dn>

## 🔗父注释

[![n350071 image](img/c9dd0fa7ece3b4a6c3e55d373898cd1e.png)](/n350071) [## 我的 Rails 笔记

### n350071🇯🇵9 月 11 日 193 分钟阅读

#rails](/n350071/my-rails-note-47cj)

## 🤔什么是 rubocop？

📚 [rubocop 文档](https://rubocop.readthedocs.io/en/latest/)T3】

> RuboCop 是一个 Ruby 静态代码分析器(又名 linter)和代码格式化程序。开箱即用，它将执行社区 Ruby 风格指南中概述的许多准则。

## 🤔怎么用？

将`.rubocop.yml`放在 app 目录下，运行以下命令。
T3】

```
bundle exec rubocop --config .rubocop.yml 
```

然后，得到这样的结果。

```
path/to/file.rb::11.1  C: Layout/TrailingWhitespace: Trailing whitespace detected.
273 files inspected, 1 offenses detected 
```

表示 rubocop 在 row11，1char 的行尾检测到应该删除的空白，违规的只有 273 个文件中的 1 个。

## 🤔怎么修？

​

### 1。用谷歌搜索检测到的消息

当你用[布局/TrailingWhitespace](https://www.rubydoc.info/gems/rubocop/RuboCop/Cop/Layout/TrailingWhitespace) 谷歌时，你会看到下面的解释，然后修复它。

```
# The line in this example contains spaces after the 0.
# bad
x = 0
​
# The line in this example ends directly after the 0.
# good
x = 0 
```

​

### 2。自动更正

使用`-a`选项。

```
bundle exec rubocop -a 
```

您可以在上找到其他选项📚[文件](https://rubocop.readthedocs.io/en/latest/auto_correct/)。