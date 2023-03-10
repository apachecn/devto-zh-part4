# 如何用预提交钩子创建一个自动化的代码清理器

> 原文：<https://dev.to/umr55766/how-to-create-an-automated-code-cleaner-with-pre-commit-hook-3j85>

我们有一个带有代码的回购协议，其中一些部分的格式不正确。这就是我们如何开始清理代码库的故事，最终，使用 git 预提交钩子。也许我们不再需要这些了(现在，ide 已经内置了这些),但是我的座右铭是展示我们如何能够自动化&改进我们的日常开发过程，而不需要或者非常少的努力。

因此，有一天，我在查看我的队友的 pull 请求时，我注意到一些小的代码格式被忽略了。这些都是非常可忽略的(对正常人来说)，所以我觉得自己解释我的想法背后的逻辑和评论来修正它们有点愚蠢。我希望我们也有一个定义好的并且双方都同意的开发风格指南，但是不幸的是，我们没有。类似这样的事情[https://thermondo.github.io/style-guide/](https://thermondo.github.io/style-guide/)

所以我想为什么我们不自动化整个代码格式化过程。因此，我们需要就代码风格格式达成一致，并找出一种以通用方式格式化的方法。

对 Python 来说幸运的是，我们有一个定义非常好的 PEP-8[https://www.python.org/dev/peps/pep-0008/](https://www.python.org/dev/peps/pep-0008/)，这是我在其他语言中很少见到的。这样，我们的第一份清单就完成了。

[![](img/00b584e1834518321ecaeb36af858988.png)](https://black.readthedocs.io/en/stable/) 其次，我们必须找出一种通用的方法来格式化我们的代码。我们发现了这颗宝石，黑色-[https://github.com/psf/black](https://github.com/psf/black)。

现在人类的行为开始发挥作用，我们已经万事俱备。一个风格指南和一个格式化程序，但是有时我们没有足够的意识在提交之前格式化我们的代码。这部分不在最初的计划中，典型的例子——为什么软件项目不能按时交付。惊喜无处不在！因此，我们将此视为一项挑战，并考虑将这部分自动化。可能会融入一些我们不会错过的东西。

我们所有人都有一个共同点，我们不能错过这一点-承诺。我们使用 git 作为我们的版本控制系统，因此我们所有人都会使用 git commit。我们如何将它集成到提交本身——git 预提交钩子拯救了我们。

我们设计了这个非常简单的预提交钩子来在提交代码之前格式化和验证我们的代码。

```
for file in $(git diff --cached --name-only | grep -E '\.(py)$')
do black "$file"
  $(git add "$file")
done

for file in $(git diff --cached --name-only | grep -E '\.(py)$')
do flake8 "$file"
  if [ $? -ne 0 ]; then echo "flake8 failed on staged file '$file'. Please check your code and try again. To check output manually run flake8 $file"
    exit 1 # exit with failure status
  fi Done 
```

Enter fullscreen mode Exit fullscreen mode

因此，这不需要人工干预。我们只需要像往常一样提交，它会自动格式化代码并验证它-有时 black 会失败或出错，所以它会在验证阶段失败，我们需要手动修复它。

为了清楚起见，在预提交钩子中，我们遍历准备提交的文件并使其变黑(打开，自动格式化它们),然后再次以同样的方式验证它们——用非错误代码退出，只是为了表明发生了错误。我们需要把这个放在我们的回购中。git/hooks/pre-commit 要让这个钩子工作，我们需要在系统中预装 black 和 flake8。

#### 学习:

1.  我们开始不担心格式化我们的代码。
2.  我们不需要担心漏掉一两行。
3.  我们在清理我们的仓库，最好的部分是无意识的。如果我们过一会儿打开一个文件，我们会觉得很神奇，我们是什么时候格式化这个文件的？厉害！
4.  我们没有用不相关的改变来膨胀提交——只是格式化那些我们无论如何都要提交的文件。被动代码清理驱动器。
5.  节省了许多开发人员的时间，他们争论是否&为什么要在行尾或下一行使用花括号。

#### 参考文献:

*   We discussed this pre-commit hook :