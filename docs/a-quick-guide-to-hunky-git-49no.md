# Hunky Git 快速指南

> 原文：<https://dev.to/jacobherrington/a-quick-guide-to-hunky-git-49no>

<sup>封面图片:[两个人拿着 Macbook Pro](https://www.pexels.com/photo/two-people-holding-macbook-pro-1181275/)by[Christina Morillo](https://www.pexels.com/@divinetechygirl)</sup>
说到 git，许多开发人员都习惯于对一个或多个文件进行修改，暂存这些文件，然后创建一个 commit。

通常，该工作流运行良好。然而，偶尔从文件的角度考虑 git 可能会导致非原子的提交。

有时，您在单个文件中进行了多次更改，而这些更改应该被分成不同的提交。例如，如果您正在为一个类添加一些功能，但是在工作过程中决定重构另一个方法，那么将这两个更改分成两次提交可能是有意义的。

您可以通过撤销和重放更改来轻松做到这一点，但是如果您的更改比几行更重要，或者您在多个文件中这样做，那么在这里使用 git 会更方便。

这是`git add --patch`的用例。正如大多数使用 git 的开发人员所知,`git add`是为将来的提交准备文件的命令。我发现许多使用 git 的开发人员不太熟悉`--patch`标志，所以我在这里解释一下。

当您遇到一个新的 git 命令或标志时，您应该做的第一件事就是询问 git:

```
git add --help 
```

Enter fullscreen mode Exit fullscreen mode

`--help`标志将提供相当多的有用文档(您应该阅读，而不是浏览),准确描述给定 git 命令的功能。

首先，我们将看到`git add`命令，“使用工作树中的当前内容更新索引，为下一次提交准备内容。”

这基本上证实了我们已经知道的东西，但是`--patch`呢？

向下滚动一点，你会看到对`--patch`(可以简称为`-p`)的描述:

```
-p, --patch
Interactively choose hunks of patch between the index and the work tree and add them 
to the index. This gives the user a chance to review the difference before adding 
modified contents to the index. 
```

Enter fullscreen mode Exit fullscreen mode

这个描述解释了一两件关于使用`--patch`标志的重要事情。首先，它允许用户“交互式地选择要上演的大片”。第二，它“给用户一个机会，在将修改的内容添加到索引中之前，查看它们之间的差异。”

虽然`git add --patch`的第一个好处非常有用，但我也发现在创建提交之前检查您所做的更改的做法很有价值。

使用`--patch`标志非常简单。

对于这个例子，假设我想在 [Solidus](https://solidus.io) gemspec 文件中修改一些东西。我将更新 gem 描述，并加入对`solidus_graphql_api`的依赖。

在做了这些更改之后，我运行`git add solidus.gemspec --patch`，我遇到了用于暂存大块的 UI。

```
solidus master % git add solidus.gemspec --patch
diff --git a/solidus.gemspec b/solidus.gemspec
index 3d8d40ed4..d585334e1 100644
--- a/solidus.gemspec
+++ b/solidus.gemspec
@@ -7,7 +7,7 @@ Gem::Specification.new do |s|
   s.name        = 'solidus'
   s.version     = Spree.solidus_version
   s.summary     = 'Full-stack e-commerce framework for Ruby on Rails.'
-  s.description = 'Solidus is an open source e-commerce framework for Ruby on Rails.'
+  s.description = 'Solidus is a neat open source e-commerce framework for Ruby on Rails.'

   s.files        = Dir['README.md', 'lib/**/*']
   s.require_path = 'lib'
Stage this hunk [y,n,q,a,d,j,J,g,/,e,?]? 
```

Enter fullscreen mode Exit fullscreen mode

我想现在是定义帅哥的时候了。

GNU diffutils 手册用这样的语言描述了一个大块头:

> 比较两个文件时，diff 会找到两个文件共有的行序列，这些行序列中散布着不同的行，称为块。

所以，简单来说，就是一行或者多行改了。暂存大块而不是文件的好处在于，它让您可以精细地控制何时暂存什么。您可以在准备阶段审查您的代码，并使您的提交更加原子化。

如果你在这个界面中，你可能会注意到提示是相当不透明的。

```
Stage this hunk [y,n,q,a,d,j,J,g,/,e,?]? 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/361bb02488beacbec908fdb7588c872f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--B2JzVvT8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttps%253A%252F%252Fmedia.giphy.com%252Fmedia%252FhyMFaxhuQkZTq%252Fgiphy.gif%26f%3D1)

谁会认为我们需要 11 个选项来回答一个是/否的问题！？幸运的是，我们可以让 git 告诉我们为什么会这样，用一个问号来回答它的问题:

```
Stage this hunk [y,n,q,a,d,j,J,g,/,e,?]? ?
y - stage this hunk
n - do not stage this hunk
q - quit; do not stage this hunk or any of the remaining ones
a - stage this hunk and all later hunks in the file
d - do not stage this hunk or any of the later hunks in the file
g - select a hunk to go to
/ - search for a hunk matching the given regex
j - leave this hunk undecided, see next undecided hunk
J - leave this hunk undecided, see next hunk
e - manually edit the current hunk
? - print help 
```

Enter fullscreen mode Exit fullscreen mode

很方便，谢谢你，饭桶！

这些选项中的大多数都是便利的特性。我特别喜欢`e`，因为它能让我看到错误，并在错误上演之前修复它们。利用这个工具应该有助于消除未来的提交，如“修复空白”或“消除打字错误”，因为这类事情可以在我准备我的更改时处理。

这看起来不错，所以我将继续进行，并与`y`阶段这一个。

然后，我进入了下一个阶段:

```
@@ -26,4 +26,5 @@ Gem::Specification.new do |s|
   s.add_dependency 'solidus_core', s.version
   s.add_dependency 'solidus_frontend', s.version
   s.add_dependency 'solidus_sample', s.version
+  s.add_dependency 'solidus_graphql', s.version
 end
Stage this hunk [y,n,q,a,d,K,g,/,e,?]? 
```

Enter fullscreen mode Exit fullscreen mode

我不想存放这个，因为我想为添加新的依赖项进行不同的提交，所以我用`q`响应提示，退出提示而不存放任何其他东西。

现在我提交对描述的修改:

```
git commit -m "Update gem description." 
```

Enter fullscreen mode Exit fullscreen mode

我可以在下一次提交时再次寻找合适的人选:

```
git add solidus.gemspec --patch 
```

Enter fullscreen mode Exit fullscreen mode

```
@@ -26,4 +26,5 @@ Gem::Specification.new do |s|
   s.add_dependency 'solidus_core', s.version
   s.add_dependency 'solidus_frontend', s.version
   s.add_dependency 'solidus_sample', s.version
+  s.add_dependency 'solidus_graphql', s.version
 end
Stage this hunk [y,n,q,a,d,K,g,/,e,?]? 
```

Enter fullscreen mode Exit fullscreen mode

我在这里看到一个错误。宝石其实叫`solidus_graphql_api`，我需要快速编辑一下。用`e`回答提示会让我进入 Vim，这样我就可以快速做出改变。

```
# Manual hunk edit mode -- see bottom for a quick guide.
  @@ -26,4 +26,5 @@ Gem::Specification.new do |s|
   s.add_dependency 'solidus_core', s.version
   s.add_dependency 'solidus_frontend', s.version
   s.add_dependency 'solidus_sample', s.version
+  s.add_dependency 'solidus_graphql_api', s.version
  end
# ---
# To remove '-' lines, make them ' ' lines (context).
# To remove '+' lines, delete them.
# Lines starting with # will be removed.
#·
# If the patch applies cleanly, the edited hunk will immediately be
# marked for staging.
# If it does not apply cleanly, you will be given an opportunity to
# edit again.  If all lines of the hunk are removed, then the edit is
# aborted and the hunk is left unchanged.
~ 
```

Enter fullscreen mode Exit fullscreen mode

在做出并保存更改之后，我回到了我的终端，我可以创建一个提交:

```
git commit -m "Add solidus_graphql_api dependency" 
```

Enter fullscreen mode Exit fullscreen mode

现在如果我们要求日志:

```
commit 2eff835db6807163f395529cfb89643a17b9b817 (HEAD -> test-git-patch)
Author: jacobherrington <redacted@gmail.com>
Date:   Tue Aug 13 10:39:56 2019 -0500

    Add solidus_graphql_api dependency

commit 70c9aab9d7a829042a35ebc9153b1b85c4e35292
Author: jacobherrington <redacted@gmail.com>
Date:   Tue Aug 13 09:30:09 2019 -0500

    Update gem description 
```

Enter fullscreen mode Exit fullscreen mode

`--patch`标志并不总是正确的工具，有时只为提交准备文件完全没问题，但特别是在更实质性、更复杂的更改的情况下，我认为使用`--patch`标志是对自己的明智检查。

采取额外的步骤来验证你做出了正确的决定总是明智的。

> 一个好的程序员总是在穿过单行道前向两边看。道格·林德

毫无关系，如果有人能告诉我道格·林德是谁，这句话最初是从哪里来的，那就太好了。🤠