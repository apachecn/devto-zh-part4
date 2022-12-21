# capybara find.text 方法忽略换行符(转换为空格)

> 原文：<https://dev.to/n350071/is-ignored-converted-to-a-space-at-capybara-find-text-3ef7>

## 🔗父注释

[![n350071 image](img/c9dd0fa7ece3b4a6c3e55d373898cd1e.png)](/n350071) [## 我的水豚笔记

### n350071🇯🇵9 月 11 日 192 分钟阅读

#rails](/n350071/my-capybara-note-2gp3)

## 🤔情况

find 方法自动地将' \n '转换成一个空格，所以 test 将为 false。

```
target = 'hello\nworld'

p target
#=> "hello\\nworld"

find('div.target').text
#=> "hello world"

expect(find('div.target').text).to eq target
#=> false 😅 
```

## 🎉解决办法

有`Capybara::RSpecMatchers#have_content`法。

*   ⭕️判断页面或节点是否有文本。
*   ❌它忽略 HTML 标签，所以如果你想测试包含 HTML 标签，你不应该使用这个。

```
expect(find('div.target')).to have_content(target) 
```

📚[水豚::RSpecMatchers](https://rubydoc.info/github/jnicklas/capybara/master/Capybara/RSpecMatchers#have_content-instance_method)