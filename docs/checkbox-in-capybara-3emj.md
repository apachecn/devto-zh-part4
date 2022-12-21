# 水豚中的复选框

> 原文：<https://dev.to/n350071/checkbox-in-capybara-3emj>

## 🔗父注释

[![n350071 image](img/c9dd0fa7ece3b4a6c3e55d373898cd1e.png)](/n350071) [## 我的水豚笔记

### n350071🇯🇵9 月 11 日 192 分钟阅读

#rails](/n350071/my-capybara-note-2gp3)

## 👍方式 1:

[水豚::Node::Actions#check](https://www.rubydoc.info/github/jnicklas/capybara/Capybara%2FNode%2FActions:check) 很简单，类似于[水豚::Node::Actions#fill_in](https://www.rubydoc.info/github/jnicklas/capybara/Capybara/Node/Actions:fill_in) 对于文本框的方式。

举例。

```
# will check a descendant checkbox with a name, id, or label text matching 'German'
page.check('German')

# will check `el` if it's a checkbox element
el.check() 
```

## 👍方式 2:变通方法

但是，当它不管用时，试着找到配对标签，然后点击它。这应该是可行，因为复选框通常与标签成对出现。

```
<input type="checkbox" value="1" name="workaround[item_id]" class="workaround" id="workaround_1"> 
```

```
find("label[for='workaround_1']").click 
```