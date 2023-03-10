# 对 Git 交互式补丁模式的全面介绍

> 原文：<https://dev.to/krnsk0/a-thorough-introduction-to-git-s-interactive-patch-mode-4bl6>

你一直在埋头做一个项目，这时你意识到:“我已经一个小时没做事了。”或者，更糟糕的是:“我的未分级的变更代表了多个[工作单元](https://jasonmccreary.me/articles/when-to-make-git-commit/)”——无论这些单元是特性、重构中的步骤还是错误修复。

假设您运行`git status`并在多个文件中看到未分级的变更。进一步假设您可以有把握地说，这些变更以一种对应于离散工作单元之间划分的方式在文件之间划分。到目前为止，一切顺利；您可以将`git add`和`git commit`分别归档，从而产生一个历史，在这个历史中，工作单元被适当地彼此分开。

但是如果代表不同工作单元的变更存在于单个文件中的*呢？假设，在你的一小时无纪律的黑客活动中，你给`app.js`中的*一个*函数添加了一个新特性，但也给同一文件中的*不同的*函数添加了一个错误修复？*

这里，`git add` ing 文件分开帮不了你；您需要能够在文件中存放和提交*区域。在 git 中，这些被称为*块*。为了处理大块，git 给了我们一个*交互式补丁模式*，我们可以用命令`git add -patch`或`git add -p`进入该模式。*

## 引入补丁模式

Patch mode 是一个小的 CLI 应用程序，它智能地将未暂存的更改分成大块，并依次将这些大块呈现给我们，以便我们可以决定是否暂存每个大块。(可以通过`git add -p`直接获得，也可以在`git add -interactive`界面内获得，不在本帖讨论。)

假设我们从一个类似于
的`app.js`开始

```
const countToNumber = number => {
  console.log(`Let's count to ${number}`);
  for (let i = 0; i < number; i += 1) {
    console.log(i);
  }
  console.log(`We counted to ${number}!`);
};

const numbers = [100, 1000, 50, 35];

numbers.forEach(number => {
  countToNumber(number);
}); 
```

Enter fullscreen mode Exit fullscreen mode

假设我们做了一些改变。在意识到`countToNumber`刚好在目标之前停止后，我们在初始化循环的那一行将`<`改为`<=`。

然后，我们决定不在这个文件中调用`countToNumber`，而是想导出一个会对控制台产生副作用的函数，以便在其他地方使用。我们将最后三行放在一个函数中，导出这个函数:

```
export const processNumbers = numbers => {
  numbers.forEach(number => {
    countToNumber(number);
  });
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们还删除了`numbers`数组，我们(可能)用它来测试我们的工作，现在我们已经准备好导出我们编写的函数了。

最后，我们决定在打印到控制台之前公开一个过滤数字的函数，所以我们添加了一个过滤函数:

```
export const filterNumbers = (numbers, maximum) => {
  return numbers.filter(n => n < maximum);
}; 
```

Enter fullscreen mode Exit fullscreen mode

做了这些改变后，我们意识到我们想把它们分开。所以，我们运行`git add -p app.js`，git 返回如下:

```
diff --git a/app.js b/app.js
index eceb575..c97e738 100644
--- a/app.js
+++ b/app.js
@@ -1,13 +1,17 @@
 const countToNumber = number => {
   console.log(`Let's count to ${number}`);
-  for (let i = 0; i < number; i += 1) {
+  for (let i = 0; i <= number; i += 1) {
     console.log(i);
   }
   console.log(`We counted to ${number}!`);
 };

-const numbers = [100, 1000, 50, 35];
+export const processNumbers = numbers => {
+  numbers.forEach(number => {
+    countToNumber(number);
+  });
+};

-numbers.forEach(number => {
-  countToNumber(number);
-});
+export const filterNumbers = (numbers, maximum) => {
+  return numbers.filter(n => n < maximum);
+};
Stage this hunk [y,n,q,a,d,s,e,?]? 
```

Enter fullscreen mode Exit fullscreen mode

> **注意**:不要过多读入输出中的`diff --git`行；`diff`命令没有这样的选项。这只是在`git diff`输出中的一个硬编码字符串[来帮助我们理解 git 正在做什么，参考我们可能拥有的关于`diff`的任何知识，并且也为这个 diff 输出指示一个*格式*(统一 diff 格式](http://www.gelato.unsw.edu.au/archives/git/0505/3880.html)[的一个变体](https://en.wikipedia.org/wiki/Diff#Unified_format))。如果你用过`git diff`，那么你在这里看到的应该是你熟悉的。
> 
> 顺便说一下，`git diff`的输出可以被重定向到一个名为*补丁*的文件中，这个补丁可以保存在某个地方或者在稍后被应用之前传递给一个朋友:`git diff > mypatch.patch`然后是`git apply mypatch.patch`。(或者，如果您想对分阶段的而不是未分阶段的变更这样做，重定向`git diff --cached`。)这与为什么我们在这里探索的工具被称为“补丁模式”有关，但是关于 git 在这方面如何工作的完整探索值得单独发布。

我们可以看到，git 已经决定将所有的更改分组到一个单独的块中。但是我们想把他们分开。我们将在后面讨论所有的命令；现在我们将选择`s`(拆分这一大块)，git 只显示我们想要的部分:

```
@@ -1,8 +1,8 @@
 const countToNumber = number => {
   console.log(`Let's count to ${number}`);
-  for (let i = 0; i < number; i += 1) {
+  for (let i = 0; i <= number; i += 1) {
     console.log(i);
   }
   console.log(`We counted to ${number}!`);
 }; 
```

Enter fullscreen mode Exit fullscreen mode

现在我们在谈话。让我们选择`y`来上演这个大块头，然后选择`q`来上演“[q]uit”。这将我们带出交互模式，回到命令行。如果我们运行`git status`，我们将会看到`app.js`中有阶段的和未阶段的变化——这正是我们想要的。我们可以使用类似`git commit -m "fix bug which stopped count just short of number"`的东西提交我们的第一个工作单元，然后准备下一个单元。

所以，我们运行`git add -p app.js`。在使用`s`进行分割之后，git 将变更隔离到`processNumbers` :

```
@@ -6,5 +6,9 @@
-const numbers = [100, 1000, 50, 35];
+export const processNumbers = numbers => {
+  numbers.forEach(number => {
+    countToNumber(number);
+  });
+}; 
```

Enter fullscreen mode Exit fullscreen mode

现在我们选择`y`，然后像以前一样选择`q`，然后我们可以提交:`git commit -m "export function which wraps countToNumber"`。我们可以回到交互模式，但是我们知道唯一剩下的未提交的变更代表一个工作单元，所以我们可以添加它并以通常的方式提交。

现在我们有了一个历史，其中我们的提交代表了离散的工作单元:

```
commit bc80191fe63bf75bae7d976b61cf1c24e9391097 (HEAD -> master)
Author: Dev.to Reader <dev.to@reader.com>
Date:   Sun Jul 28 10:48:37 2019 -0500

    export function for filtering lists of numbers

commit 6c36b351cc2a8cc0f6f3582b3221f5e427980fbc
Author: Dev.to Reader <dev.to@reader.com>
Date:   Sun Jul 28 10:47:02 2019 -0500

    export function which wraps countToNumber

commit 0b59f6917215a5c264735e4ed01cf6d1f3c977e4
Author: Dev.to Reader <dev.to@reader.com>
Date:   Sun Jul 28 10:40:25 2019 -0500

    fix bug which stopped count just short of number 
```

Enter fullscreen mode Exit fullscreen mode

## 补丁模式命令

运行`?`或`h`将显示补丁模式下可用的命令:

```
y - stage this hunk
n - do not stage this hunk
q - quit; do not stage this hunk or any of the remaining ones
a - stage this hunk and all later hunks in the file
d - do not stage this hunk or any of the later hunks in the file
g - select a hunk to go to
/ - search for a hunk matching the given regex
j - leave this hunk undecided, see next undecided hunk
J - leave this hunk undecided, see next hunk
k - leave this hunk undecided, see previous undecided hunk
K - leave this hunk undecided, see previous hunk
e - manually edit the current hunk
? - print help 
```

Enter fullscreen mode Exit fullscreen mode

我们已经看到了`y`、`q`和`s`，它们分别允许我们放置大块、退出和分割成更小的大块。我们很快就会回到分裂的话题。

通常，在进入补丁模式和分割后，我会用`jJkK`翻阅大块文件，在文件中定位自己，寻找我可以组合在一起的变化模式，然后进行一些修改并退出。

重要的是，可以在一个文件(`git add -p app.js`)或整个存储库(`git add -p`)上调用补丁模式，因此如果您在多个文件中有更改，但是您知道您想要将更改保存在一个文件中，那么您可以这样做。`a`和`d`旨在用于我们已经进入 pathspec 中许多文件的补丁模式的环境，因为它们帮助我们批量处理文件中的更改。一般来说，如果我们想要处理大量的变更，根据它们出现的文件，我们可能不会处于交互模式；因此，我发现自己有时使用`d`，而几乎从不使用`a`。

`g`命令给我们一个交互式菜单，显示所有块的文件名和行号。我也不经常使用这种方法，因为我通常不知道在将它们呈现给我之前，我对暂存哪些文件和行感兴趣。

然而,`/`命令非常有用。当我经历了很多变化，发现一些工作我想组合在一起共享一些共同的可搜索特性——比方说，对一个特定变量或函数的引用——我会使用`/`和`y`减少变化，直到`/`停止返回结果。

选择`e`将在您配置 git 使用的编辑器(`git config --global core.editor`)中打开当前块，以允许逐行暂存。当你已经达到了交互模式给你的最小块大小，并且需要对提交的内容进行非常精细的控制时，这是非常有用的。我们一会儿再回到这个话题。

## 意图添加

如果您向存储库中添加一个新文件，添加一些内容，然后运行`git diff`，您将不会在输出中看到新文件的任何内容。这是因为`git diff`向我们展示了相对于暂存区的未暂存的变更，如果我们还没有跟踪一个文件，那么它还不能用于比较。

因为交互式添加模式是围绕`git diff`构建的，所以对新的未跟踪文件的更改最初在补丁模式下是不可见的。我们当然可以`git add`这个文件，但是这导致了一个难题，因为现在我们已经暂存了整个文件。有几种方法可以解决这个问题。

一个不太传统的解决方案可能是使用`git reset -p`——因为`git reset`也有一个交互式补丁模式。我们可以用它来取消新文件中除了我们想要提交的更改之外的所有更改，然后提交。

但是这很麻烦。一个更传统、更快速的解决方案是使用命令`git add --intent-to-add`(或者它的简写`git add -N`)。这允许我们将文件*而不是其内容*添加到暂存区，这意味着`git diff`和`git add -p`会给我们想要的东西。

## 开发者工作流程&大块头的极限

对`e`的偶尔需求显示了交互式补丁模式的一些局限性。[选项](https://git-scm.com/docs/diff-config#diff-config-diffcontext)用于`git diff`让我们为块指定一些缺省值:`git config --global diff.context`让我们设置上下文的行数以提供变化，而`diff.interHunkContext`选项让我们设置块之间应该出现的行数，允许我们对块的大小进行一些控制。

但是，尽管我们得到了对缺省值的控制，git **总是**将相邻行的更改分组在一起。如果连续行上的变更代表不同的工作单元，我们将需要使用`e`手动编辑补丁，这给了我们对提交内容最细粒度的控制。

补丁有一些与 git 本身紧密相关的限制:git 中最小的变更单位是一行，所以如果我们对一行进行了多次表示不同工作单位的变更，git 不能帮助我们分别记录这些变更。

但这只是说，交互式补丁模式不是替代品，也不是有组织的开发过程。虽然它可以帮助我们从偶尔的会话中恢复过来，在这些会话中，我们没有执行任务或团队要求我们执行的 git 纪律，但是它可能像任何工具一样被误用或过度使用。所以，不要让`git add -p`的可用性取代你工作流程中的思考和计划。