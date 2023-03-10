# 使用 Vim + SourceKit-LSP

> 原文：<https://dev.to/rderik/using-vim-sourcekit-lsp-1hc4>

我选择的编辑一直是`vim`。不是因为它是最好的，可能是因为它是我用得最多的。由于多年的使用，所有的命令都已经印在了我的脑海里。所以毫不奇怪，我会尝试使用 SourceKit-LSP 为 Swift 文件使用`autocomplete`和`jump-to-definition`。

几个月前我写了一篇关于设置的文章。但是有些人在从主分支构建`SourceKit-LSP`时遇到了麻烦。

因此，如果您使用 Xcode11，以下是解决方案。

切换到分支:`swift-5.1-branch`并从该分支开始构建。

```
#In the directory where you cloned the SourceKit-LSP repo.
$ git fetch
$ git checkout swift-5.1-branch
$ swift build 
```

Enter fullscreen mode Exit fullscreen mode

那么一切都应该正确构建。

如果你想查看设置的原始文章，你可以在这里找到:

[https://rderik.com/blog/setup-swift-lsp-and-vim/](https://rderik.com/blog/setup-swift-lsp-and-vim/)