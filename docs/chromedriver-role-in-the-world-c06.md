# chromedriver 在世界中的角色

> 原文：<https://dev.to/tonetheman/chromedriver-role-in-the-world-c06>

chromedriver 一生只有一项工作，那就是为 Selenium 运行 Chrome。它与 Chrome 紧密相连，并由 Chrome 进行版本控制。换句话说，你需要 chromedriver 版来运行 Chrome 75。如果你为你的测试升级 Chrome，那么你也需要升级 chromedriver。

要使用 chromedriver，您需要在调用 Chrome 构造函数时将它包含在源代码中。或者您需要将它包含在脚本环境中使用的 PATH 变量中。

我将展示两者的例子。

下面是你如何在源代码中使用 chromedriver 的路径。

```
 cpath = "e:\\projects\\headless\\chromedriver.exe"
    driver = webdriver.Chrome(cpath) 
```

Enter fullscreen mode Exit fullscreen mode

这里是你如何设置路径并做同样的事情。假设 chromedriver.exe 位于目录 e:\projects\headless 中

这是在命令提示符下或者在您用来运行测试的 bat 文件中完成的。(此处仅 WINDOWS 批处理文件)

```
set PATH=e:\projects\headless;%PATH% 
```

Enter fullscreen mode Exit fullscreen mode

如果您在 Linux 或 OSX 上运行，您将在 bash 中使用这个语法。

```
export PATH=/home/yourhome/projects/headless:$PATH 
```

Enter fullscreen mode Exit fullscreen mode

这是你在那种情况下启动 Chrome 的代码。

```
 driver = webdriver.Chrome() 
```

Enter fullscreen mode Exit fullscreen mode

Selenium 的 Python 语言绑定需要知道 chromedriver 的位置。您可以显式或隐式地告诉绑定。两个都可以。

当我最初为自己写脚本时，我倾向于使用基于路径的脚本。