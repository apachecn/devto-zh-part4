# 用 run 命令启动 git 二等分

> 原文：<https://dev.to/matthewbdaly/powering-up-git-bisect-with-the-run-command-3ajn>

当试图捕捉任何回归时，Git 中的`bisect`命令非常有用。如果您知道某个 bug 在过去的某个时候不存在，而现在存在，那么您可以经常使用`bisect`来快速、轻松地追踪它。

基本功能相当简单。您通过追踪过去已知的“好的”提交和已知的“坏的”提交(通常是分支的负责人)来开始这个过程。然后，你开始平分:

```
$ git bisect start 
```

Enter fullscreen mode Exit fullscreen mode

然后指定错误的提交:

```
$ git bisect bad HEAD 
```

Enter fullscreen mode Exit fullscreen mode

还有你的好承诺

```
$ git bisect good fe0616f0cd523455a0e5bc536c09bfb1d8fd0c3f 
```

Enter fullscreen mode Exit fullscreen mode

然后它将遍历两次提交之间的所有提交。请注意，并不是每个提交都被加载——而是从您输入的提交中挑选一个提交，并从那里迅速缩小范围。对于每一次提交，您都要对其进行测试，并根据情况用`git bisect good`或`git bisect bad`将其标记为好或坏。一旦跟踪到导致问题的提交，它将告诉您该提交是什么，从而使任何剩余的调试变得容易得多。有些情况更难处理，比如数据库迁移已经创建并在中间运行，但是在很多情况下`bisect`可能是一个非常有价值的工具。

然而，手动执行这些提交仍然是一件麻烦的事情。幸运的是，在您可以生成某种脚本来确定问题是否存在的情况下，有一种简单的方法可以使用`bisect run`命令来实现自动化。

我现在正在进行的一个个人项目是一个微型 CMS，主要用于小册子风格的网站。它包括一个在前端使用 Fuse.js 的 AJAX 搜索，其索引由构建在 Symfony 控制台组件之上的控制台任务生成。最近我注意到，尽管单元测试仍然通过了，但是由于 Flysystem 的问题，生成索引的控制台任务不再像预期的那样工作。因为它在控制台中抛出了一个错误，所以它可以用作`git bisect`的输入。因此，我可以通过运行下面的命令来自动查找 bug:

```
$ git bisect run php console index:generate 
```

Enter fullscreen mode Exit fullscreen mode

这是非常罕见的，因为这是一种理想的情况——问题是控制台命令抛出了一个显式错误，这是对`bisect run`的完美输入。在许多情况下，更有可能的情况是，如果您想要自动捕捉错误，您将需要创建一个自动测试来重现该错误，并使用`git bisect run`运行该测试。鉴于 TDD 已经建议在修复 bug 之前编写一个测试来重现 bug，谨慎的做法是首先编写测试，然后在修复 bug 并提交修复和新测试之前使用它来运行等分命令，这样不仅可以最大限度地减少所需的手工工作，还可以确保它不会再次出现。

某些类别的问题更难以这种方式自动化——例如，CSS 中的可视化回归。如果您使用 React 或 Vue 之类的库，快照测试可能是自动化组件呈现的 HTML 的二分过程的好方法，或者您可以尝试我之前提到的 PHP 应用程序快照测试方法。对于由于迁移历史中的间隙而无法创建和拆除数据库进行测试的遗留应用程序来说，确保运行之间的一致性也很棘手和耗时。然而，如果你能做到这一点，自动化平分命令会使它更快，并留给你一个测试，你可以保留，以确保错误不再出现。