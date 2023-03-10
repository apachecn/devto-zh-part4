# YARN”错误:没有场景；必须至少有一个。

> 原文：<https://dev.to/rishiabee/yarn-error-there-are-no-scenarios-must-have-at-least-one-ndd>

在 Ubuntu 中，执行命令`yarn`会给出这个奇怪的错误消息:

```
ERROR: There are no scenarios; must have at least one. 
```

我的朋友，你弄错了😄

对照已发布的版本[https://github.com/yarnpkg/yarn/releases](https://github.com/yarnpkg/yarn/releases)
，检查您系统上的`yarn`版本

```
yarn -v 
```

如果版本差异很大，那么你可能有一个`yarn`包，它是 Unix 命令行工具的[cmd test](https://liw.fi/cmdtest/)T3】黑盒测试的一部分。

⚠️清除现有纱线(风险自担)⚠️

```
sudo apt-get -remove yarn 
```

并继续执行来自 [yarnpkg](https://yarnpkg.com/en/docs/install#debian-stable) 的指令

简化说明:

*   从添加纱线**开始适当的**包装库

```
curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add - 
```

*   然后进行更新和安装

```
sudo apt-get update && sudo apt-get install yarn 
```

*   最后，重新检查版本

```
yarn --version 
```

*快乐黑客*🔨