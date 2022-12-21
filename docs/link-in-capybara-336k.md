# 点击水豚中的链接

> 原文：<https://dev.to/n350071/link-in-capybara-336k>

## 🔗父注释

[![n350071 image](img/c9dd0fa7ece3b4a6c3e55d373898cd1e.png)](/n350071) [## 我的水豚笔记

### n350071🇯🇵9 月 11 日 192 分钟阅读

#rails](/n350071/my-capybara-note-2gp3)

## 👍方式 1:通常的方式。

[水豚::Node::Actions#click_link](https://www.rubydoc.info/github/jnicklas/capybara/Capybara%2FNode%2FActions:click_link) 很简单。

你可以直接输入文本。这个例子点击 dev.to edit 链接。

```
click_link('EDIT') 
```

还有，你可以这样做。

```
click_link(href: "/n350071/my-capybara-note-2gp3/edit") 
```

但是，如果页面有 2 个或 3 个相同的链接呢？您将面临模糊匹配错误。

## 👍方式 2:变通方法

您可以使用 find 和 find_all 方法。

```
find('a[href="/n350071/my-capybara-note-2gp3/edit"]') 

find_all('a[href="/n350071/my-capybara-note-2gp3/edit"]').each do
  # do something
  # back
  page.go_back
end 
```

* * *

## 如果要点击按钮，

```
click_button('SUBMIT') 
```