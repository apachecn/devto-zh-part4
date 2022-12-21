# 调试(也)Python 中的标准库

> 原文：<https://dev.to/iaziz786/debugging-also-the-standard-library-in-python-1mk4>

我们都必须经常调试我们的应用程序，以便修复一个错误，学习一些新的东西，或者理解我们的代码流，等等。

你有没有试过用 Python 调试你的代码，更确切地说是用 VS 代码调试？

如果你有，那么你会发现 VS 代码不允许你进入标准库包。我想知道在这些文件下发生了什么，以便更好地理解为什么我的代码不能像预期的那样工作。

我正试着用`socket`库`create_connection`。每次当我的文件运行时，我都会得到这个错误。

[![](img/d3ff49058b69b23b39ca7c41d3b57ff8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3qIQTT4M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lris08ut9m0knmpz8ry7.png)

对我没什么帮助。我想把断点放在抛出错误的地方。现在的问题是如何做到这一点？

它有两个步骤:

1.  如果没有配置文件，请添加一个。
2.  将`justMyCode`加到`false`上。🎉

### 1。添加配置文件

做起来很简单。

*   只需转到`Debug`选项卡
*   点击`No Configurations`选项
*   选择`Add Configuration...`

在那之后，只需选择任何一个提示选项，在我的例子中是`Python File`，但是 VS 代码也支持像`Django`、`Flask`和`Pyramid`等框架。

[![](img/8bea55c83a572a40e10d1ff7bd5443e4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--t-V5Khxi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kw9kttn4hoflo0w23d5x.png)

完成后，你会看到一个`.vscode`文件夹，在这个文件夹中，会有一个名为`launch.json`的文件。

### 2。添加魔法单词😄

现在是时候添加那些神奇的单词了，它们将使你能够调试文件而不是你的代码。

在`launch.json`中，选择一个配置并添加`justMyCode: false`。就是这样！

[![](img/449e59bb3f0886789a6da715a00053ae.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KZePlODL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3z1y4tx77xdrefqdqw4m.png)

VS 代码现在将允许您在标准库的代码中漫游，并且您可以找到您的代码不能按预期工作的确切原因。

在我的例子中，问题是当你将`host`和`port`传递给`socket.create_connection`时，你没有预先考虑`https://`。

### 脚注

在推特上关注我

封面图片:[克里斯在](https://unsplash.com/@cdr6934) [unsplash](https://unsplash.com/photos/ieic5Tq8YMk) 上尝试