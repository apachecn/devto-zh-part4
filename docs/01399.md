# 在水豚中附加一个文件

> 原文：<https://dev.to/n350071/attach-a-file-in-capybara-52ke>

## 🔗父注释

[![n350071 image](img/c9dd0fa7ece3b4a6c3e55d373898cd1e.png)](/n350071) [## 我的水豚笔记

### n350071🇯🇵9 月 11 日 192 分钟阅读

#rails](/n350071/my-capybara-note-2gp3)

## 🤔情况

你想把一个文件附加到输入标签上。

```
<label for='data-file'>Data File</label>
<input type="file" name="files" id="attach_files"> 
```

## 👍常规解决方案

[水豚::Node::Actions # attach _ file](https://www.rubydoc.info/github/jnicklas/capybara/Capybara/Node/Actions#attach_file-instance_method)

```
attach_file('data-file', 'path/to/file.csv') 
```

## 🦄变通解决方案

```
find('form input[type="file"]').set('path/to/file.csv') 
```

📚[附上水豚的档案——今天才知道](https://til.hashrocket.com/posts/c790268652-attach-a-file-with-capybara)