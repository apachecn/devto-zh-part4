# 在 Capybara Rails 测试中使用“在”缩小范围

> 原文：<https://dev.to/n350071/narrow-down-scope-with-within-in-capybara-rails-testing-14hi>

## 🔗父注释

[![n350071 image](img/c9dd0fa7ece3b4a6c3e55d373898cd1e.png)](/n350071) [## 我的水豚笔记

### n350071🇯🇵9 月 11 日 192 分钟阅读

#rails](/n350071/my-capybara-note-2gp3)

## 🤔通过使用查找方法

```
expect(find('div.greet > h1').text).to eq 'Hello'
expect(find('div.greet > p.message').text).to eq "world" 
```

## 👍通过在方法中使用

```
within('div.greet') do
  expect(find('h1').text).to eq 'Hello'
  expect(find('p.message').text).to eq "world"
end 
```

📚[方法:Capybara::Session # within—jnicklas/Capybara(master)的文档](https://www.rubydoc.info/github/jnicklas/capybara/Capybara%2FSession:within)