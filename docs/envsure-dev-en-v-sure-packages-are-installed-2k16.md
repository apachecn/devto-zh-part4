# envsure:确保软件包已安装

> 原文：<https://dev.to/negue/envsure-dev-en-v-sure-packages-are-installed-2k16>

## 让我们从*为什么*开始

当在 ~~a~~ 的一些项目中使用`storybook`时，我看到了大量需要安装的软件包(依赖项/插件)。当然在本地回购上，安装一次这个可以忽略。

但是当您将它推送到您的 CI 中，并为不同类型的测试使用不同的 CI 运行程序时，您可能不需要每次都为每个 CI 安装 storybook(和 addons)。良好的..如果你想测试你的组件的话？但是即使这样，也只有这个特定的 CI 作业需要安装软件包。

### 变通方法 1: `package.json`(我用过一个)

你可以创建`package.json`-脚本-任务，比如:
`"install-stuff": "npm install myPackage@1 otherPackage@2 andSo@0n"`

这对于 3-4 个包来说可能是可以的，但是当不得不添加 8 个以上的包并且不得不在你的`package.json`中的一行中处理所有的版本时就不行了。就我而言，我不想再做那件事了

### 解决方法 2: `install-other-packages.sh`

在 shell 脚本中使用相同的`npm install`,确保您可以使用多行来简化概述。

如果你有一组你需要一次的包，这是可以的，但是不同的组呢？

### 解决方法 3:

在你的 repo 中使用一个不同的文件夹(它有自己的`package.json`包含这些需要的包)

这些解决方法对我来说都不够好。所以我就自己做了。

## 介绍`envsure` -不寻常的(节点)包安装程序插件

你可以通过全球安装`npm install -g envsure`或者使用`npx`来使用`envsure`

例如:
或`npx envsure groupName`

它会使用你当前目录下的`envsure.json`来安装`groupName`需要的所有包，而不会把它们添加到 package.json.

```
{  "version":  1,  "groupName":  {  "packageA":  "1.2.3",  "packageB":  "2.3",  "and-so-on":  "1"  }  } 
```

`version`:(可选)属性用于确定 json 模式，以防将来更新。

你也可以把它和你通常的 npm 脚本一起使用

```
...  "premy-routine":  "npx envsure groupName",  "my-routine":  "..."  ... 
```

链接:[回购](https://github.com/negue/envsure) | [npm](https://www.npmjs.com/package/envsure)

这是我做的第一个 cli。因此，我可能没有任何关于使用基于节点的 cli 的最佳实践。很抱歉。

另外，这可能只是一个利基用例，但也许它会帮助一些人。