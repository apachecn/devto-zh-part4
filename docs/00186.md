# Bash 中的本地别名

> 原文：<https://dev.to/weirdmayo/local-aliases-in-bash-3ej9>

**快速故事**

最近开始学 Rust。所以我运行了大量的`cargo check`、`cargo run`和`cargo build`命令。我想把它缩短为`ccheck`和`crun`之类的。

只是，我的机器上已经有类似的命令了。我在 Rust 项目中从来不需要它们，所以我想把这些命令重新分配给这些小别名。

**这篇文章为什么存在**

没有办法设置“本地别名”命令。如果在我的 bash 概要文件中定义了 alias，它将在任何地方使用。所以我创造了一个对我有用的小技巧，也可能对你有用

**解决方案**

所以，实现这一点的方法是通过 Bash 函数。在 bash 函数中，我们可以检查我们当前所在的目录，并采取相应的措施。

```
function ccheck(){
    if [[ $PWD == "/home/daniel/rust-projects/" ]]; then
        # if you are in this folder, execute this command
        cargo check;
        # return stops the function, and it won't reach the ccheck call later in this function
        return;
    fi ccheck;
} 
```

Enter fullscreen mode Exit fullscreen mode

但是您立即面临的问题是，稍后的`ccheck`“实际的那个”将会中止您的 bash 会话，因为它将陷入递归中。

为了防止这种情况发生，你只需要在你想要执行的命令前加上`command`。这样，您就不会调用函数，而是调用实际的命令。

```
function ccheck(){
    # stuff...

    command ccheck;
} 
```

Enter fullscreen mode Exit fullscreen mode

**现在怎么办？**

比较目录的一个更好的方法是对照正则表达式。但是由于我们的小目录并不复杂(或者`cargo`可以在项目中的任何地方运行，呵呵)，我们可以用简单的通配符比较
来代替 regex

```
if [[ $PWD == *"rust-projects"* ]]; then
    # ...
fi 
```

Enter fullscreen mode Exit fullscreen mode

这将使该别名在该目录中的任何地方都有效。

希望对你有帮助:)