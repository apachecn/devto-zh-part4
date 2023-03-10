# 我如何在一个废弃的图书馆里处理一个 Bug

> 原文：<https://dev.to/marcofiset/how-i-dealt-with-a-bug-in-an-abandoned-library-17b6>

免责声明:这几乎是我第一次处理这样的问题。如果你对此有任何建议，欢迎在评论中分享:)

* * *

在每个开发人员的职业生涯中，总会有这样一个时候，人们会发现不是在他们自己的代码中，而是在他们使用的库中存在一个 bug。这种情况过去在我身上发生过好几次，但在这种特殊情况下还是第一次。

大多数时候，当我在一个库中遇到一个 bug 时，我发现它最终在某个更新版本的库中被修复了。我更新了我的 package.json 文件，`npm install`并修复了我的 bug。有时需要通读变更日志来识别任何潜在的重大变更，这通常是在公园散步。

然而，这一次不同了。

我用的版本是 4 年前的。Github 库上有 100 多个问题，还有 30 多个 Pull 请求有待合并。有一个新版本正在开发中，但最后一次提交是在 10 个月前。我的 bug 根本没有可能很快被修复，更不用说如果我决定自己修复它的话会被合并。

## 点击叉按钮的时间

我查看了代码，因为这是一个我已经很熟悉的库，所以我知道应该去哪里找。

我找到了 2 行有问题的代码(实际上是在同一个文件的 2 个不同地方的完全相同的行)，修补它们，提交，然后推到我自己的回购分支。现在，Github 就在那里，向我唠叨我的分支版本，它比源代码库提前了一次提交。出于好的原因，我肯定会花时间为我的 bug 修复编写测试，并向作者提交一份 PR，即使我不希望它很快被合并。

现在是时候在我的项目中使用我的补丁库了。嗯，原来`npm`可以直接从 Github 下载库。你所要做的就是打开你的`package.json`文件，并修改你想要替换的库的版本号:

`"some-library": "git+https://github.com/username/package.git#tag"`

其中`username`是您的 Github(或您的组织)名称，`package`是存储库名称，`tag`引用特定提交的 SHA-1 号或标签。

现在你可能认为你的麻烦就要过去了，你是对的！但是不要抱太大希望，因为你可能会遇到一些问题🙃

此时，我在我自己版本的库和实际发布的库之间来回移动，以确保我的更改正常工作。窃听器还在。哦，不。无论我做了什么，改变我的 package.json 文件或删除我的 node_modules 目录并重新安装，都会安装错误版本的库。

原来`npm`和几乎所有的包管理器都使用某种形式的依赖版本锁定机制。这意味着无论何时你安装一个依赖项，你安装的确切版本将被记录到一个文件中(在`npm`的例子中是`package-lock.json`，如果提交到你的库(你一定要这么做)，将确保团队中的每个人使用你的项目所依赖的每个包的确切版本。

为了安装我自己的版本，我需要做的是打开 package-lock.json 文件，并取出引用该特定库的 json 片段。现在`npm install`可以正确地从我的 Github 库获取数据了。

还有惊喜！虫子不见了！我很快将存储库的所有权转移到我的组织，并相应地更新了我的 package.json 文件。为将来更多的错误库和分叉库干杯🍻🤓

欢迎在评论中讨论一些你可能遇到的类似情况！我和任何喜欢这篇文章的人都非常感谢任何见解。

* * *

希望这篇文章对你有用😇

如果是的话，请考虑在这里和 Twitter 上关注我，了解更多这类内容！我致力于分享我在这里学到的东西，不管它对更高级的你来说是多么微不足道。干杯！