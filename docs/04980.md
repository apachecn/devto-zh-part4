# 您应该考虑在提交消息中添加更多的细节

> 原文：<https://dev.to/oliverjumpertz/you-should-consider-adding-more-detail-to-your-commit-messages-l7g>

无论你是独自工作还是与团队一起工作，我很确定我们每个人都写过这样的提交消息:

```
$ git commit -m "Fixed some stuff" 
```

Enter fullscreen mode Exit fullscreen mode

或者你至少可以这样写你的提交消息:

```
$ git commit -m "Added a new endpoint for trucks" 
```

Enter fullscreen mode Exit fullscreen mode

在某些时候，您的 git 日志可能会这样结束:

```
commit ae43284df673cdd3fa14b96766fd3c6fe2a06051 (HEAD -> master)
Author: Oliver Jumpertz <you@me.com>
Date:   Sat Aug 31 13:45:37 2019 +0200

    Added a new method to calculate a cats' weight

commit 28773ac45fda5111d7258789492fc4a7836c9411
Author: Oliver Jumpertz <you@me.com>
Date:   Tue Aug 13 11:31:43 2019 +0200

    Fixed a bug

commit b5ee1d9c09545b03470bb96f64cbad220bc30754
Author: Oliver Jumpertz <you@me.com>
Date:   Thu Aug 1 13:43:37 2019 +0200

    Fixed some stuff

commit 573af7670d7bcb19c77fbf323e34fbfb536037ee
Author: Oliver Jumpertz <you@me.com>
Date:   Mon Jul 29 15:22:45 2019 +0200

    Initial commit 
```

Enter fullscreen mode Exit fullscreen mode

## 一些基本知识

### 提醒你什么是 git

Git，首先是一个去中心化的版本控制系统。它的目的是让你和其他人一起工作代码，而不必在过程中复制文件和覆盖彼此的修改(是的，这可能真的被简化了)。它还能让你把你的代码推到一个或多个(是的，git 是去中心化的，记得吗？)远程位置，在那里其他人可以访问您的更改，并将他们的更改分别推送到。

### 提交

您对存储库所做的每一个更改都是一个提交，并且您可以对您所做的每一个提交进行描述。这是提交消息。
每当您看到对存储库的更改时，提交消息应该会为您提供某种关于更改的上下文。

Git 也有一个很好的特性，那就是`$ git blame <file>`用关于**的信息来注释文件中的每一行，这些信息是关于谁**在的时候对那一行**做了最后一次修改，以及用**提交**。
现在大多数编辑器和 ide 都允许你直接在其中注释代码，添加提交消息，并允许你直接在编辑器中区分不同的版本，所以你不需要切换到你的终端然后再返回来，并且可以推断出**为什么**那行或那块代码是现在这个样子。**

## 我们可以用它来做什么？

### 为什么你看到的代码是这样的原因

#### 一种可能的情景

随着项目的增长和时间的推移，我们往往会忘记它的某些部分。也许我们经常在不同的项目之间切换，或者我们的代码库太大了，以至于我们从来没有在一个任务中接触过所有的东西。大多数时候我们在一个团队中工作，每个人都在为代码库做贡献。

在某些时候，我们可能会偶然发现我们或其他人以前编写的一些代码。这可能是偶然的，也可能是有意的，因为我们调试了 REST API，它并没有返回我们所期望的所有猫，而是到达了我们现在试图找到我们的 bug 的函数:

```
async function getCats() {
  const cats = await fetchHouseCats();
  return cats.map(cat => Object.assign({}, cat, {type: 'house cat'}));
} 
```

Enter fullscreen mode Exit fullscreen mode

为什么我们只抓家猫？野猫呢？在那个时刻，我们只能推测为什么那个函数是这样实现的。当我们进一步观察时，我们看到另一个函数:

```
async function getWildCats() {
  const cats = await fetchWildCats();
  return cats.map(cat => Object.assign({}, cat, {type: 'wild cat'}));
} 
```

Enter fullscreen mode Exit fullscreen mode

这个函数的名字似乎更好，因为它反映了它实际在做什么。获取我们的野猫，给它们适当的类型，然后返回它们供进一步使用。

让我们看看我们的 git 历史，并尝试找出这是该代码的第一次迭代，还是它在过去看起来有所不同。
当我们这样做的时候，我们会看到这个旧版本的代码:

```
async function getCats()
{
  const houseCats = await fetchHouseCats();
  const typedHouseCats = houseCats.map(cat => Object.assign({}, cat, {type: 'house cat'}));

  const wildCats = await fetchWildCats();
  const typedWildCats = wildCats.map(cat => Object.assign({}, cat, {type: 'wild cat'}));

  return typedHouseCats.concat(typedWildCats);
} 
```

Enter fullscreen mode Exit fullscreen mode

通过使用`$ git blame cats.js`(或者我们的编辑器等价物)，我们发现 Mark 在上周四修改了那部分代码。现在是星期一，他正在休假，因此无法联系到他。
提交消息内容如下:

```
commit f7fb3f38aa67699bf815a8e28455ac541a8a0e52
Author: Mark Turboprop <mark@yours.com>
Date:   Thu Aug 22 15:57:12 2019 +0200

    Split up getCats into house cats and wild cats. 
```

Enter fullscreen mode Exit fullscreen mode

变化是故意发生的，这是肯定的。然而，他似乎既没有重命名旧的 getCats 函数，也没有改变它在我们的/cats REST 端点中的用法。通过跟踪 getWildCats 的用法，我们发现确实有两个新的 API:/house cats 和/wildcats。每个都使用其中一个功能。但是马克并没有创造出一种新的方法，将这两种功能结合起来，使/cats 仍然工作。

由于我们没有任何进一步的信息，并且在接下来的三周内无法联系到 Mark，我们必须进一步挖掘，尝试找到 Mark 处理过的问题，这需要一些时间，并发现确实存在对单个 API 的功能请求，同时保留旧 API 的功能。

为了修复我们的 bug，我们将 getCats 重命名为 getHouseCats，并实现了一个新函数 getCats，它将两者结合起来，使/cats 再次工作。然后，我们确保所有三个 API 现在都正常工作，就到此为止。

#### 我们本可以节省很多时间

我们能够修复这个错误，让我们的用户再次开心起来**但是**我们花了比应该花的更多的时间。
首先，我们必须假设为什么事情发生了变化，然后我们必须切换到浏览器，在我们的票证中进行长时间的搜索，直到我们最终找到**为什么**特定代码发生变化的实际原因。

让我们未来的生活变得更容易的第一个改进将是实际上包括一个我们用来跟踪我们的任务的任何系统的参考。提交可能是这样的:

```
commit f7fb3f38aa67699bf815a8e28455ac541a8a0e52
Author: Mark Turboprop <mark@yours.com>
Date:   Thu Aug 22 15:57:12 2019 +0200

    CATS-1234: Split up getCats into house cats and wild cats. 
```

Enter fullscreen mode Exit fullscreen mode

有了提交消息开头的引用，我们就能够直接进入我们选择的系统，打开故事，看看请求了什么，然后将其与实现的内容进行比较。

根据您是哪种类型的开发人员，这可能对您来说已经足够了，没关系。它必须为你和你的同事工作。

就我个人而言，我更喜欢尽可能多的信息直接放在手边，而不需要在不同的应用程序之间切换。

在 git 中还有另一种方式来执行提交。
如果你正在使用 bash，你可以这样做:

```
$ git commit -m 'This is the first line
this is the second line
this is the third line
...
' 
```

Enter fullscreen mode Exit fullscreen mode

或者，如果您的命令行不允许多行字符串带有单引号，您可以这样做:

```
$ git commit -m "Head line" -m "Content" 
```

Enter fullscreen mode Exit fullscreen mode

向提交消息添加更多细节。我们一会儿会谈到为什么和什么。

第三个选项是用
为 git 提交消息配置您选择的编辑器

```
$ git config --global core.editor "vim" 
```

Enter fullscreen mode Exit fullscreen mode

同时用最适合您的编辑器替换 vim。

从那时起你就可以

```
$ git commit 
```

Enter fullscreen mode Exit fullscreen mode

并在该编辑器中编辑实际的提交消息。

因为我们现在可以添加更多的信息，所以我们可以在必要的时候为我们和其他人提供更多的见解。

我喜欢使用的一个方案如下:

```
commit f7fb3f38aa67699bf815a8e28455ac541a8a0e52
Author: Oliver Jumpertz <you@me.com>
Date:   Thu Aug 22 15:57:12 2019 +0200

    <ticket-ref>: <head-line describing shortly and as best as possible what was done>
    <blank-line>
    <what-you-actually-did> because of <reason-why-you-did-it> which leads to <outcome>
    <blank-line>
    <possible-drawbacks-or-implications> 
```

Enter fullscreen mode Exit fullscreen mode

这里有一个例子:

```
commit f7fb3f38aa67699bf815a8e28455ac541a8a0e52
Author: Oliver Jumpertz <you@me.com>
Date:   Thu Aug 22 15:57:12 2019 +0200

    PROJ-9876: Changed the sorting implementation of getTrucks to a stable one.

    Changed the sorting implementation of /trucks from QuickSort to MergeSort to prevent trucks with identical horse 
    power but different ids, which were later added, changing the position of those trucks in the resulting JSON 
    between calls because some legacy clients frequently run into errors with their response caching when positions of 
    already known trucks change. The new sorting should now produce stable results, permanently, 
    and thus fix the problem with legacy clients.

    Memory usage in peak times might go up a little and should be monitored. 
```

Enter fullscreen mode Exit fullscreen mode

我们现在有三种类型的信息。我们有一个标题行，说明我们处理了什么问题，并简要描述完成了什么。
如果这还不够，如果有必要，你可以使用更详细的描述块。
第三块给我们提供了一些见解，无论是谁做了实现或更改，都知道一些含义并考虑过它。它也可以用来记录我们以后可能要做的事情。

### 为你的下一个版本创建一个变更日志

由于我们现在已经极大地改进了提交消息的数量和质量以及其中的信息，所以仅仅从提交消息中生成一个 changelog 是相对容易的。
如果你使用上面的模板，只需提取中间部分:

```
[...]
<what-you-actually-did> because of <reason-why-you-did-it> which leads to <outcome>
[...] 
```

Enter fullscreen mode Exit fullscreen mode

您可以非常准确地说出自从存储库最后一次被标记为发布以来做了什么。

## 结论

无论你是以开发为生还是仅仅作为一种爱好，总是要想到未来的自己和与你一起工作的人。没有人愿意花太多的时间去寻找他们需要的信息，或者在别人可能已经告诉他们的情况下做一些假设。