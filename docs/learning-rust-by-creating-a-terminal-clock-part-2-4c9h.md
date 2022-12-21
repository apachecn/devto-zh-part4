# 通过创建终端时钟学习 Rust 第 2 部分

> 原文：<https://dev.to/bmitch/learning-rust-by-creating-a-terminal-clock-part-2-4c9h>

在上一篇文章中，我刚刚添加了这个月的进度条。我还注意到代码急需清理，但我还没有做到。我在添加新功能和学习新东西上享受了太多的乐趣。尽管我最终还是会到达那里。

## 添加配置项管道

自从上一篇文章以来，我完成的主要工作之一就是使用 Travis CI 为项目添加 CI 管道。这是我以前在各种 PHP 项目中做过的事情，但不是用 Rust。我使用 Travis CI 也有一段时间了，但它使用起来非常简单，没有花很长时间就能再次理解。

基本上，我只需要在 Travis CI 中启用我的 GitHub 存储库，并提交一个`.travis.yml`文件，我已经在这里完成了:

[https://github.com/bmitch/relogio/blob/master/.travis.yml](https://github.com/bmitch/relogio/blob/master/.travis.yml)

使用该配置，Travis CI 将在管道中运行以下命令:

```
 - cargo build --verbose --all
  - cargo test --verbose --all 
```

## 开源之力

我也有一个人参与了这个非常棒的项目。大声喊到

[![ntrupin image](img/083800af13cc8bb72701ee395cb5e989.png)](/ntrupin)

## [诺亚·特鲁平](/ntrupin)

[V and Swift.](/ntrupin)

[t0【转储】T2【转储】T4【转储】T5【转储】T6【转储】T7】https://转储. com【转储】T8](https://twitter.com/ntrupin)

为了帮助。自从我上一篇文章以来，他们做出了一些贡献，非常感谢。

## 年进度

最后，我为今年的倒计时增加了一个进度条。我想这大概会是最后一个进度条了。我不确定我是否会添加世纪倒计时进度条。

## 接下来

*   我真的需要做一些代码清理。
*   我想研究其他可能用于 CI 渠道的工具(linters、代码嗅探器、代码质量等)...)
*   原型化一些 UI 设计思路。
*   为其他可能的功能添加一些更多的问题。

一如既往，感谢所有反馈！