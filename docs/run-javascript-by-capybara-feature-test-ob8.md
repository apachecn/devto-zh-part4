# 运行 Javascript by Capybara 功能测试

> 原文：<https://dev.to/n350071/run-javascript-by-capybara-feature-test-ob8>

## 🔗父注释

[![n350071 image](img/c9dd0fa7ece3b4a6c3e55d373898cd1e.png)](/n350071) [## 我的水豚笔记

### n350071🇯🇵9 月 11 日 192 分钟阅读

#rails](/n350071/my-capybara-note-2gp3)

## 🤔情况

假设您有以下 iframe。而且，当 iframe 高度太短时，您希望断言 iframe 中的选择器不会显示。

```
<div>
  <iframe src='https://n350071.com' id='n350071-frame'></iframe>
</div> 
```

## 👍解决办法

对水豚使用 **execute_script** 方法。

```
# run a script
page.execute_script("$('#n350071-frame').height(10)")

# then, test it.
within('n350071-frame') do
  expect(find('div.body').not_to have_selector('div.target')
end 
```

语法是这样的。

```
execute_script(script, *args) 
```

## 💚还有一个技巧:evaluate_script

如果需要返回值，请使用 evaluate_script。但是，要小心，它可能会返回复杂的 jQuery 对象。所以，默认情况下应该使用 execute_script。

```
evaluate_script(script, *args) 
```

注意:当 jQuery 的复杂返回值返回时，没有返回值的 execute_script 较好。

## 📚参考

*   [执行 _ 脚本](https://rubydoc.info/search/github/jnicklas/capybara/master?q=execute_script)
*   [评估 _ 脚本](https://rubydoc.info/search/github/jnicklas/capybara/master?q=evaluate_script)