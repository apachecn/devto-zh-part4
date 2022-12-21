# Chrome 扩展的 Selenium 测试失败

> 原文：<https://dev.to/tonetheman/headless-selenium-testing-with-chrome-extensions-is-broke-af-m0j>

如果你想知道，你不能用 Selenium 和 headless Chrome 测试扩展。Chrome 不支持这种类型的测试。

```
options = webdriver.ChromeOptions()
# print(dir(options)) 
# see this https://stackoverflow.com/questions/45372066/is-it-possible-to-run-google-chrome-in-headless-mode-with-extensions
# you cannot test extensions if you are in headless mode?
# it is a bug options.add_argument('headless') 
```

但是好消息是，您可以轻松地加载一个带有 Selenium 的扩展。只要你不是无头的。

```
options.add_argument("--load-extension={0}".format("e:/projects/headless/myext")) 
```

另一件事我发现至少在 Windows 10 与这个版本的 chromedriver

```
ChromeDriver 75.0.3770.140 (2d9f97485c7b07dc18a74666574f19176731995c-refs/branch-heads/3770@{#1155}) 
```

每次测试后，我都会得到一个错误的 chromedriver.exe 进程(我使用的是 Python 绑定)。

为了解决这个问题，我改用了 webdriver。仅用手跑过一次 chromedriver.exe。