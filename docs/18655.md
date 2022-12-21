# 如何使用 GIT 挂钩强制执行传统的提交消息

> 原文：<https://dev.to/prahladyeri/how-to-enforce-conventional-commit-messages-using-git-hooks-2bmk>

*[原载 prahladyeri.com](https://prahladyeri.com/blog/2019/06/how-to-enforce-conventional-commit-messages-using-git-hooks.html)*

传统的 git 提交消息不仅很好，而且非常棒。事实上，一旦你开始了解它们，你会开始觉得它们在任何严肃的编程项目中都是必不可少的。例如，考虑以下两个提交消息之间的差异:

```
git commit -m "added social login feature for authentication using twitter"
git commit -m "feat(authentication): added social login using twitter" 
```

Enter fullscreen mode Exit fullscreen mode

第二个版本不仅可读性更好，而且它遵循的标准使得与 Travis CI 和 Appveyor 等构建工具的集成更加容易。不仅如此，当您的`git log`如下所示时，您可以轻松地自动生成`CHANGELOG`:

```
> git log --oneline
61c8ca9 (HEAD -> master) fix: navbar not responsive on mobile
479c48b test: prepared test cases for user authentication
a992020 chore: moved to semantic versioning
b818120 fix: button click even handler firing twice
c6e9a97 fix: login page css
dfdc715 feat(authentication): added social login using twitter 
```

Enter fullscreen mode Exit fullscreen mode

总而言之，我非常喜欢传统的提交消息，所以我不想让它成为可选的，而是让它成为默认的生活方式。我该怎么做？答案很简单: *git 钩子*。

你不需要成为一个忍者或者专家来使用钩子。只需知道在您的特殊 git 存储库文件夹(名为`.git`)中，还有一些其他的特殊文件夹:

```
logs
hooks
objects
refs 
```

Enter fullscreen mode Exit fullscreen mode

我们感兴趣的是`hooks`。要掌握它，在您的机器上创建一个测试项目，并通过运行以下命令初始化一个空的 git 存储库:

```
git init . 
```

Enter fullscreen mode Exit fullscreen mode

现在访问刚刚生成的`.git`文件夹，并导航到 hooks 文件夹。会有一堆扩展名为`.sample`的脚本(也就是说都被禁用了)。我们感兴趣的是`commit-msg`。这是在提交之前触发的钩子，如果消息格式不正确，它允许您通过抛出错误来拒绝提交。

在这个目录中创建一个名为`commit-msg`的新脚本，并使用您最喜欢的编辑器(我的是 notepad++)将下面的代码(您需要安装 python)添加到其中！):

```
#!/usr/bin/env python import re, sys, os

def main():
    # example:
    # feat(apikey): added the ability to add api key to configuration
    pattern = r'(build|ci|docs|feat|fix|perf|refactor|style|test|chore|revert)(\([\w\-]+\))?:\s.*'
    filename = sys.argv[1]
    ss = open(filename, 'r').read()
    m = re.match(pattern, ss)
    if m == None: raise Exception("conventional commit validation failed")

if __name__ == "__main__":
    main() 
```

Enter fullscreen mode Exit fullscreen mode

保存脚本并使其可执行(在 linux 上运行`chmod u+x commit-msg`，在 windows 上不需要)。现在回到您初始化 git repo 的源代码文件夹，创建一个用于测试的源文件。然后，`git add`并尝试使用非常规消息提交测试。如果一切顺利，应该会这样失败！

```
> git commit -m "added a new feature for xyz"
Traceback (most recent call last):
  File ".git/hooks/commit-msg", line 22, in <module>
    main()
  File ".git/hooks/commit-msg", line 19, in main
    if m == None: raise Exception("conventional commit validation failed")
Exception: conventional commit validation failed 
```

Enter fullscreen mode Exit fullscreen mode

现在用一个有效的提交消息进行测试，它应该工作了！

一旦你练习了几次并掌握了它，你就会想在将来使用`git init`的任何项目中默认这个行为，不是吗？当然，你可以通过创建一个全局 git commit hook 模板来做到这一点，但那将是以后的事了。重要的事情先来！

**编辑**

对于那些真正对执行和自动化这件事感兴趣的人，我刚刚发布了一个名为 [enforce-git-message](https://github.com/prahladyeri/enforce-git-message) 的 python 包。你所需要做的就是安装 python 包，它会自动将上面的脚本复制到你的`~/.git-templates`目录，并设置`git config init.templatedir`的值。

```
pip install enforce-git-message 
```

Enter fullscreen mode Exit fullscreen mode

参考资料:

*   [https://www.conventionalcommits.org/en/v1.0.0-beta.4/](https://www.conventionalcommits.org/en/v1.0.0-beta.4/)
*   [https://stack overflow . com/questions/3523534/good-ways-to-manage-a-changelog-using-git](https://stackoverflow.com/questions/3523534/good-ways-to-manage-a-changelog-using-git)
*   [https://stack overflow . com/questions/37699261/how-to-validate-and-display-error-for-git-commit-message](https://stackoverflow.com/questions/37699261/how-to-validate-and-display-error-for-git-commit-message)
*   [https://coder wall . com/p/jp 7d 5 q/create-a-global-git-commit-hook](https://coderwall.com/p/jp7d5q/create-a-global-git-commit-hook)