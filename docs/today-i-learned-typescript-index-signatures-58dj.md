# [今天我学会了]打字稿索引签名

> 原文：<https://dev.to/protium/today-i-learned-typescript-index-signatures-58dj>

几天前，我读了一篇文章，标题中有一句话“今天我学到了”。所以我想提出这个话题作为一个倡议，这个话题**今天我学习了**或**直到**来激励我们所有人分享那些**一两行**代码，这些代码是**完成了**，那些**命令**是你一直在寻找的，并且对其他开发人员有用。

**编辑:**已经以“todayilearned”存在，爽！

## 指标签名

我正在写一些与 **GitHub Gists API** 交互的代码。尤其是这个端点[https://developer.github.com/v3/gists/#get-a-single-gist](https://developer.github.com/v3/gists/#get-a-single-gist)得到一个简单的要诀。
注意字段`files`是一个**对象**而不是一个**数组**。那么，如何安全地为这个 find 响应定义类型呢？我们不知道有多少个属性以及每个属性的名称。

一个小小的谷歌查询指引我找到了这个 docs [TypeScript 索引签名](https://basarat.gitbooks.io/typescript/docs/types/index-signatures.html)。

因此，该 Gists 响应的类型应如下

```
interface GistFile {
    filename: string;
    type: string;
    language: string;
    raw_url: string;
    size: number;
    truncated: boolean;
    content: string;
  };

interface GistsFiles {
    [key: string]: GistFile
}

interface GistsResponse {
...
files: GistsFiles
... 
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以访问这样的响应

```
console.log(gistFile.files.somefile.filename);
console.log(gistFile.files['somefile'].filename); 
```

Enter fullscreen mode Exit fullscreen mode

我们开始吧。希望这对你有用。