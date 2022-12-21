# 在水豚中测试 css(图标)

> 原文：<https://dev.to/n350071/test-css-icon-with-capybara-5fb8>

## 🔗父注释

[![n350071 image](img/c9dd0fa7ece3b4a6c3e55d373898cd1e.png)](/n350071) [## 我的水豚笔记

### n350071🇯🇵9 月 11 日 192 分钟阅读

#rails](/n350071/my-capybara-note-2gp3)

## 🤔情况

假设你用[字体牛逼](https://fontawesome.com/icons/map-marker?style=solid)。然后，您想要测试页面上是否只有一个地图标记图标可见。

换句话说，你想找到 css。

```
<i class="fas fa-map-marker"></i> 
```

## 👍解决办法

还有`Capybara::Node::Matchers#has_css?`。您还可以指定可见计数。

像这样。

```
expect(has_css?('i.fa-map-marker', count: 1)).to eq true 
```