# git 钩子和爱斯基摩犬

> 原文：<https://dev.to/hi_iam_chris/git-hooks-and-huskyjs-13gb>

当使用 Git 时，像今天的大多数项目一样，代码经历 4 个阶段。这些阶段是:

*   无人探索过的
    *   文件/更改是可见的，但不会被提交。
*   阶段
    *   通过执行 git add 命令，更改进入这种状态。一旦保存了更改，在下一次提交时将会提交这些更改。
*   坚定的
    *   在执行 git commit 命令后，这些更改将被提交，并通过 git push 发送到主存储库。如果在提交之后进行了更改，那么必须使用 git add 命令再次添加这些更改。
*   推
    *   通过执行 git 推送，更改被发送到远程服务器。

这一切听起来非常清楚和简单，但有时，只是从一个阶段到另一个阶段是不够的，可能还需要一些其他的行动。这可以是向团队的其他成员发送邮件通知代码被推送，在推送之前运行测试或者在每次提交之前执行代码分析。这就是 Git 钩子非常有用的地方。

### 什么是 git 钩子？

> Git 挂钩是 Git 在提交、推送或合并等事件之前或之后执行的脚本。

简单来说，钩子是自动触发的动作。这些动作是 bash 脚本，一旦 git 项目被初始化，就可以在。git/hooks 文件夹。在这个文件夹中，已经有了每个事件的脚本，它们可以作为钩子的例子。可以为其定义挂钩的事件有:

*   appliypatch-msg
*   应用前补丁
*   应用后补丁
*   预提交
*   准备-提交-消息
*   提交消息
*   提交后
*   pre-rebase
*   结账后
*   合并后
*   预接收
*   更新
*   接收后
*   更新后
*   预自动气相色谱
*   重写后
*   预推

这是一个不同事件的长列表，其中大多数您永远都不需要使用。原因可以从钩子被分成的两类中找到。这些类别是客户端和服务器端挂钩。客户端是在本地机器上执行的，比如预提交和后提交，服务器端是在中央机器上执行的，比如预接收。

遗憾的是，git hooks 上的资料不多，但可能是最好的，最完整的是 githooks.com。这个站点包含了所有钩子和链接的例子，这些钩子和链接指向许多其他使用它们或帮助使用它们的库和项目。

虽然这些听起来都很棒，但是钩子还有一些问题需要考虑。

*   钩子是 bash 脚本，现在大多数开发人员都不习惯编写 bash。
*   钩子位于。git/hooks 文件夹，这意味着它们是不可克隆的，每个开发人员都需要手动将它们添加到 hooks 文件夹中。

### 沙哑

作为这些问题的解决方案，我们可以使用 Husky 库。Husky 是 npm 库，有助于轻松创建和管理挂钩。

### 要求:

节点> = 8 . 6 . 0 版

去吧

安装程序

Npm

```
npm install husky –save-dev 
```

Enter fullscreen mode Exit fullscreen mode

故事

```
yarn add husky 
```

Enter fullscreen mode Exit fullscreen mode

添加第一个挂钩:

要将挂钩与 husky 配合使用，需要完成以下步骤:

1.  创造。项目根目录下的 huskyrc 文件
2.  在这个文件中创建一个带有属性钩子的对象，它将包含所有的钩子
3.  在对象下添加钩子，其中键是事件名，值是要执行的命令。

### 举例:

```
// .huskyrc
{
  "hooks": {
    "pre-commit": "echo \"Hello, pre-commit hook!\""
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，在提交之前，我们应该在命令行中看到文本“你好，预提交钩子！”。这是一个非常简单的例子，不会有太大的用处。然而，我们也可以运行不同的东西。我们可以运行 npm 任务、另一个 bash 脚本或某个节点脚本。

NPM 任务示例:

```
“pre-commit”: “npm run test” 
```

Enter fullscreen mode Exit fullscreen mode

NodeJS 示例:

```
“pre-commit”: “node script.js” 
```

Enter fullscreen mode Exit fullscreen mode

Bash 脚本:

```
“pre-commit”: “sh script.sh” 
```

Enter fullscreen mode Exit fullscreen mode

当这些配置被添加时，husky 将为我们创建挂钩。此外，由于配置文件被添加到项目根，它可以被所有团队成员跟踪和使用。这比在 hooks 文件夹中编写 bash 脚本并发送给每个人要简单得多。

### 自定义挂钩

在编写定制钩子时，进程的返回值很重要。如果你的定制钩子需要阻止进入下一阶段，它需要用任何非零值退出。如果它以值 0 退出，阶段将被视为成功，并将继续下一步。Bellow 是 bash 脚本的一个例子，如果它在主分支中，它将阻止提交，否则它将允许提交。

```
#!/bin/sh

branch = " $(git rev-parse-abbrev-ref HEAD)"

#   要防止 git 推送的分支名。在这种情况下，它是“主人”

if[" $ branch " = " master "]；然后
附和“你不能直接提交到' " ${branch}"' branch" 
出口 1 
 fi 
```

Enter fullscreen mode Exit fullscreen mode

### 
  
哈士奇的问题

哈斯基也有一些问题需要考虑。其中之一是将其添加到现有项目中。在将 husky 添加到现有 npm 项目时，会出现无法正确创建挂钩的情况。我发现有效的解决方案是删除 node_modules 和 yarn.lock 和/或 package.lock 文件并重新安装所有依赖项。第二个可能发生的问题是持续时间。虽然钩子只增加几毫秒时间，但是它们触发的进程可以持续很长时间。例如，在每次提交之前运行测试。触发它是短暂而琐碎的，但执行它们可能需要很长时间。

### 结论

正如你在上面看到的，钩子可以帮助改进开发过程。有了哈士奇在上面，这就更容易了。需要注意的限制很少，但是，可能性只受到开发人员创造力的限制。
在[这个 github 库](https://github.com/kristijan-pajtasev/git-husky-demo)中可以找到样本很少的小演示项目。