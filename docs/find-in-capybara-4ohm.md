# 在水豚中发现

> 原文：<https://dev.to/n350071/find-in-capybara-4ohm>

## 🔗父注释

[![n350071 image](img/c9dd0fa7ece3b4a6c3e55d373898cd1e.png)](/n350071) [## 我的水豚笔记

### n350071🇯🇵9 月 11 日 192 分钟阅读

#rails](/n350071/my-capybara-note-2gp3)

## 基本

### 👍按属性查找(按名称查找)

```
find('input[type="checkbox"]').click
find('a[href="/dashboard"]').click
find('[name="user_form[age]"]') 
```

### 👍按名称查找

```
find('a', name: 'click here').click 
```

### 👍按元素和类查找

```
# <div class='my-class'>hello</div>
find('div.my-class').text

# <div id='book-body'>
#    <div class='book-contents vertical'>にほんご</div>
# </div>
find('#book-body > book-contents.vertical').text 
```

### 👍查找不可见元素

```
find('p.message', visible: false).text 
```

## 高级

### 👍查找节点的父节点

```
# just one parent node
find('#target_node').find(:xpath, '..')

# recursively parent
el.find(:xpath, '../../../dt')
el.find(:xpath, 'ancestor::dl') 
```

📚[递归向上或向下| XPath 教程](https://docs.scrapy.org/en/xpath-tutorial/topics/xpath-tutorial.html#recursively-go-up-or-down)

### 🤔从几个相同的选择器中查找

#### 👻错误代码

如果只使用查找方法，您将会看到不明确的匹配错误。

```
<div class='base'>
  <span class='greeting'>hello</span>
  <span class='greeting'>goodbye</span>

  <a href="#">link_1</a>
  <a href="#">link_2</a>
</div> 
```

```
find('span.greeting')
#=> Capybara::Ambiguous: Ambiguous match, found 2 elements matching visible css ('span.greeting') 
```

#### 👍好代码

```
first('div.hello')
all('div.hello') 
```

*   骗局
    *   容易的
*   赞成的意见
    *   它们不会等待选择器被渲染。
    *   如果将来选择器顺序被修改，您必须维护。

📚[堆栈溢出](https://stackoverflow.com/questions/13132506/capybara-ambiguity-resolution)

#### 🦄好代码

```
find('div.base > span:nth-child(1)').text #=> hello
find('div.base > span:nth-child(2)').text #=> goodbye

find('div.base > span:nth-of-type(1)').text #=> hello
find('div.base > span:nth-of-type(2)').text #=> goodbye

find_link('link_1', match: :first).cilck 
```

n-of-type 更好。

📚[第 n 个孩子](https://developer.mozilla.org/en-US/docs/Web/CSS/:nth-child)