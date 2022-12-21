# have_selector 适合水豚等待测试

> 原文：<https://dev.to/n350071/haveselector-is-good-for-waiting-test-in-capybara-5g5d>

## 🔗父注释

[![n350071 image](img/c9dd0fa7ece3b4a6c3e55d373898cd1e.png)](/n350071) [## 我的水豚笔记

### n350071🇯🇵9 月 11 日 192 分钟阅读

#rails](/n350071/my-capybara-note-2gp3)

## 🤔情况

让我们假设您有一个面板，并测试它从打开变为关闭。

## 😅错误代码

```
expect(find('div.target > div.message', visible: false).visible?).to eq true
find('div.target').click # the panel will be closed.
expect(find('div.target > div.message', visible: false).visible?).to eq false

# Error because Capybara is too fast 😭
# ---
# Failure/Error
#   expected: false
#        got: true 
```

## 👍好代码

我们用[Capybara::RSpecMatchers # have _ selector](https://www.rubydoc.info/github/jnicklas/capybara/Capybara%2FRSpecMatchers:have_selector)吧。

然后，水豚等待动画，直到测试为绿色。

```
expect(find('div.target').to have_selector('div.message')
find('div.target').click
expect(find('div.target').not_to have_selector('div.message') 
```