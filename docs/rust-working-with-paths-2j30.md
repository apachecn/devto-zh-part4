# Rust 使用路径工作

> 原文：<https://dev.to/adam_cyclones/rust-working-with-paths-2j30>

很多年前，我在 node.js 中第一次接触到文件系统和路径的运行时。

不得不佩服 node.js 中的 path 模块的简洁。我不得不假设其他语言不会有很大的不同。

今年我尝试了 c++(即使它变得非常非常冗长和难以理解，我还是有点喜欢它)。

上周我拿起了 Rust，我想要 c++，但要有一张友好的脸。到目前为止，我得到了通常的摇摆不定，“我应该放弃吗？又换语言？这也很难，不过我喜欢这个速度，我可以理解我写的一些东西……”但是我坚持在那里，我试图写一个简单的 CLI 工具，除了将一个输入路径连接到当前的工作目录之外什么也不做，想想`pwd + input_path`。在节点中，这是微不足道的，我可以像这样解析路径:

```
const userInput = argv // maybe something like this "../";
path.resolve(process.cwd(), userInput); // noice! 
```

Enter fullscreen mode Exit fullscreen mode

但是谷歌搜索没有让我更接近，货物没有板条箱(包装)，我可以理解为具有类似的解决功能。

我的选择:你帮助我认识到我做错了什么。我也承认铁锈不适合我。
2:尝试 V lang
3:使用 LuaJit
4:缓慢但高效地使用 Typescript

我不太确定。