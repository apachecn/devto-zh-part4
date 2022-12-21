# 如何使用 GitHub wiki 加速代码评审

> 原文：<https://dev.to/rrjoson/how-to-use-github-wiki-to-speed-up-code-reviews-4kcm>

当进行代码评审时，您可能会发现自己不得不指出团队编码风格中的不一致，而这些不一致是 linter 无法发现的。比如命名变量、文件结构等等。有时不得不这样做，并且不得不解释为什么一种编码风格优于另一种编码风格会影响 PR 被合并的时间。

解决的方法之一是创建一个 GitHub wiki。如果您有一个将数组命名为`nameList`而不是`names`的标准，请将它记录在那里。如果您的团队更喜欢导入 lodash 函数，如`import _get from 'lodash/get;'`而不是`import get from 'lodash/get';`，请在那里添加它。但是在你把它放在那里之前，确保你已经和你的团队讨论了利弊。

由于两个原因，结果是更快的代码审查过程。首先，在进行代码评审时，您可以使用 wiki 作为指南。第二，当一个新的开发人员进来时，一种编码风格没有被遵循，你可以参考 wiki，而不必解释为什么一种编码风格优于另一种。