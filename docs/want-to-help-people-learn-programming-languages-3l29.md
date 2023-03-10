# 想帮人学习编程语言？

> 原文：<https://dev.to/sleeplessbyte/want-to-help-people-learn-programming-languages-3l29>

exercisem 是一个在线平台，旨在通过实践和指导帮助您提高编码技能。它目前提供超过 50 种语言的*。*

 *[exercisem](https://exercism.io)为您提供数千种不同语言的练习。一旦你开始学习一门语言，你就会看到一系列需要完成的核心练习。每一个都是有趣的挑战，旨在教你更多关于语言的特性。

在写这篇文章的时候，我是 [JavaScript](https://exercism.io/tracks/javascript) 和 [TypeScript](https://exercism.io/tracks/typescript) track 和[的维护者，不久前](https://exercism.io/blog/track-anatomy-project)我们已经开始致力于将体验的一部分自动化。

[![sleeplessbyte image](img/e2378c71f742f90ce1a160234cb9e5f3.png)](/sleeplessbyte) [## 用 TypeScript 编写代码分析器(从头开始)

### Derk-Jan 打来的电话 10-19-17 分钟后开始读

#typescript #showdev #opensource #tutorial](/xpbytes/writing-a-code-analyzer-in-typescript-5ec3)

上面的帖子广泛地讨论了如何编写一个分析器以及这项工作的内容。

作为 **Hacktoberfest** 的一部分，我更新了 [`javascript-analyzer`](https://github.com/exercism/javascript-analyzer/blob/master/CONTRIBUTING.md) 和 [`javascript` -track](https://github.com/exercism/javascript/blob/17ed7e7487fd2a6f243b9bd304797e99dc4f0fa2/CONTRIBUTING.md) 投稿指南。其他维护者也对他们的存储库做了同样的事情。

# 你能做什么

因为在[exercisem](https://exercism.io)有如此多的项目在运行，所以你也有很多工作可以做。以下是我觉得最有趣的事情的简短列表。

我有很大的偏见，但是有 160 个存储库，并且在过去的一个月中至少有 90 个是活跃的，这对任何人和每个人来说都是足够的。

## 帮你说出你最喜欢的语言

鉴于我们有超过 50 首*语言*曲目，你最喜欢的语言可能是其中之一。他们中的许多都有开放的问题，并且*所有的*都允许*的新练习*从其他轨道移植过来。

# [![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) 清单:可以实施的练习 #660](https://github.com/exercism/javascript/issues/660) 

[![SleeplessByte avatar](img/65ed1c8adf9923e3ff5396ce124fac3a.png)](https://github.com/SleeplessByte) **[SleeplessByte](https://github.com/SleeplessByte)** posted on [<time datetime="2019-04-04T11:58:55Z">Apr 04, 2019</time>](https://github.com/exercism/javascript/issues/660)

嗨，有新的贡献者。

其工作方式是你可以通过下面的链接找到问题的详细说明。这是我们这次练习的标准数据。如果你想知道这个练习看起来是什么样的，看看其他实施这个练习的赛道。你可能想从类似的语言开始，比如 [`typescript`](https://github.com/exercism/typescript) 和 [`coffeescript`](https://github.com/exercism/coffeescript) ，然后可能是其他类似的语言，比如 [`ruby`](https://github.com/exercism/ruby) 或 [`python`](https://github.com/exercism/python) 。

1.  打开一个新问题，我们将用`new exercise ✨`对其进行标记
2.  我们会把这个问题分配给你，所以你可以开始做这个练习
3.  在`/exercises`中创建新文件夹
4.  您需要用匹配的配置文件`sync`这个文件夹。可以用 [`scripts/sync`](https://github.com/exercism/javascript/blob/master/scripts/sync) 来做到这一点:`ASSIGNMENT=slug npx @babel/node scripts/sync`。
5.  创建一个`<slug>.js`存根文件。
6.  创建一个`<slug>.spec.js`测试文件。如果可能的话，根据规范数据在这里添加测试。
7.  创建一个`example.js`文件。放置一个*工作的*实现，假设它被重命名为`<slug>.js`
8.  使用 [`scripts/test`](https://github.com/exercism/javascript/blob/master/scripts/test) : `ASSIGNMENT=slug npx @babel/node scripts/test`在本地运行测试。
9.  开个 PR。

**注意:**使用[此](https://tracks.exercism.io/javascript/master/unimplemented)链接查看 <g-emoji class="g-emoji" alias="zap" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/26a1.png">⚡️</g-emoji> 未实施练习的实时列表。

[View on GitHub](https://github.com/exercism/javascript/issues/660)

## 写一个分析器，或者帮助改进一个

目前有 **17** (或更多)分析仪！这意味着许多赛道*没有*任何分析仪基础设施。如果你精通一门语言，你可以帮上忙。但是，我希望您能在 [`javascript-analyzer`](https://github.com/exercism/javascript-analyzer/blob/master/CONTRIBUTING.md) 或 [`typescript-analyzer`](https://github.com/exercism/typescript-analyzer/blob/master/CONTRIBUTING.md) 上提供帮助。

*   为现有练习编写一个完整的新分析器。我们将提供 500 个`fixtures`和什么*应该*批准，什么不应该。我们会帮你写一些初始信息，你不用担心。
*   帮助改进现有的分析仪。在接下来的一周，将会有更多的问题被提出来指出需要改进的地方！

# [![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) 提高电阻-颜色-duo:检测一元+  #56](https://github.com/exercism/javascript-analyzer/issues/56) 

[![SleeplessByte avatar](img/65ed1c8adf9923e3ff5396ce124fac3a.png)](https://github.com/SleeplessByte) **[SleeplessByte](https://github.com/SleeplessByte)** posted on [<time datetime="2019-09-26T13:57:39Z">Sep 26, 2019</time>](https://github.com/exercism/javascript-analyzer/issues/56)

**描述改进**:

当一个解决方案有`+expression`而不是`Number(expression)`，并且是*而不是最优*时，我们想要给出一个特定的信息。应该还是*认可*，但是加个信息`Number(...)`优先于一元`+`。

**哪个练习**

`resistor-color-duo`

[View on GitHub](https://github.com/exercism/javascript-analyzer/issues/56)

# [![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) 提高电阻-颜色-duo:检测串串联 #55](https://github.com/exercism/javascript-analyzer/issues/55) 

[![SleeplessByte avatar](img/65ed1c8adf9923e3ff5396ce124fac3a.png)](https://github.com/SleeplessByte) **[SleeplessByte](https://github.com/SleeplessByte)** posted on [<time datetime="2019-09-26T13:52:09Z">Sep 26, 2019</time>](https://github.com/exercism/javascript-analyzer/issues/55)

**描述改进**:

当提供以下主要方法时，分析器提前退出:

```
export const value = arr => {
  return +(COLORS.indexOf(arr[0]) + "" + COLORS.indexOf(arr[1]));
};
```

而是应该被`hasDigitsString`抓住，产生同样的评论。

**哪个练习**

`resistor-color-duo`

**附加上下文**

`ResistorColorDuoSolution`有一个方法来检查练习*是否有一个构造好的字符串*。它目前只检测到一个类似于`${a}${b}`的字符串模板文字，但是我们还想捕获以下内容:

```
`${a}` + `${b}`
a + "" + b
a + '' + b
""  + a + b
''  + a + b
a.toString() + b
a.toString() + b.toString()
a.toString().concat(b)
```

[View on GitHub](https://github.com/exercism/javascript-analyzer/issues/55)

# [![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) 添加 rna 转录分析仪 #53](https://github.com/exercism/javascript-analyzer/issues/53) 

[![SleeplessByte avatar](img/65ed1c8adf9923e3ff5396ce124fac3a.png)](https://github.com/SleeplessByte) **[SleeplessByte](https://github.com/SleeplessByte)** posted on [<time datetime="2019-09-26T11:40:39Z">Sep 26, 2019</time>](https://github.com/exercism/javascript-analyzer/issues/53)

本期为`javascript`赛道 [`rna-transcription`](https://github.com/exercism/javascript/tree/master/exercises/rna-transcription) 核心练习的讨论和作业。

<g-emoji class="g-emoji" alias="link" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f517.png">🔗</g-emoji> [实施](https://github.com/exercism/javascript/tree/master/exercises/rna-transcription) | [导师-笔记](https://github.com/exercism/website-copy/tree/master/tracks/javascript/exercises/rna-transcription/mentoring.md) | [问题-规范](https://github.com/exercism/problem-specifications/tree/master/exercises/rna-transcription)

* * *

这项练习的重点是

*   [`String`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String) 迭代( [`String#replace`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/replace) ， [`String#split`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/split) )
*   [`Object`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object) / `Map`

## 最优解

```
const TRANSCRIPTION = {
  C: 'G',
  G: 'C',
  A: 'U',
  T: 'A',
};

export function toRna(sequence) {
  return sequence
    .split('')
    .map(nucleotide => TRANSCRIPTION[nucleotide])
    .join('')
}
```

变体包括`Set`或`Map`带`#get`，有效！

### 变化(可审批无意见)

也可以使用字符串析构:

```
return [...sequence].map(/**/).join('')
```

[字符串#替换](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/replace)也可以使用:

```
return sequence.replace(/./g, (nucleotide) => /* */)
```

变化包括仅替换“已知的”核苷酸:

```
sequence.replace(/[CGAT]g/, (nucleotide) => /* */)
```

### 助手功能变异

可以使用辅助函数来代替匿名函数:

```
function transcribe(nucleotide) {
  return TRANSCRIPTION[nucleotide]
}
```

这也允许没有*映射*对象的版本:

```
function transcribe(nucleotide) {
  switch(nucleotide) {
    case 'C': { return 'G' }
    case 'G': { return 'C' }
    case 'A': { return 'U' }
    case 'T': { return 'A' }
  }
}
```

## 应该评论而不应该评论

我们*应该*用该分析仪评论的案例:

*   使用 [`Object`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object) 来跟踪映射，而不是条件句。
*   通过 [`String#split`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/split) 或 [`String#replace`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/replace) 进行迭代，而不是将`for` / `forEach`与 [`Array#push`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/push) 一起使用
*   不鼓励 [`Array#reduce`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce) 使用这个特定的解决方案，因为它创建了许多中间字符串(比`split`方法更多)，除非解决方案的其余部分是正确的(这时你可以提出来，但要批准)。使用`reduce`需要读者进行更多的解释，以遵循、改变和维护。
*   不鼓励用 foreach 迭代 [`String#substring`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/substring) ，因为通过`split('')`的字符迭代比用 1 的`substring`更惯用，更易维护。使用`split('')`需要读者更少的解释来跟随、改变和维护。

## 走近

建议的方法是:

*   检测是哪种*类型*的解决方案(切换、映射或 for/forEach)
*   确保*最优解*被正确处理。您可以使用`batch` runner 为所有夹具生成输出。
*   从那里开始，添加不赞成的路径。

[View on GitHub](https://github.com/exercism/javascript-analyzer/issues/53)

## 加入导师

自然，我们总是在寻找新的导师。你不需要成为你的语言专家，但是我们希望你有丰富的经验。你将*在*人的旅程中指导他们学会流利。我们还有一个`#mentor-the-mentor`项目，帮助你更好地进行指导。

## 加入学员！

知道如何用 Java 编程，但一直想学 Rust？或者，也许你终于迈出了知道 TypeScript 的那一步？一些曲目目前已经被超额预订，但总的来说，我们欢迎任何人成为学生。注:这是*而不是*一个*教你如何编码的平台*。这是一个平台，可以让你流利地掌握一门新的语言。*