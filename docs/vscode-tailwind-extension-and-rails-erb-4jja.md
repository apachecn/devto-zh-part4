# VSCode 顺风延伸和轨道(ERB)

> 原文：<https://dev.to/scottw/vscode-tailwind-extension-and-rails-erb-4jja>

如果在 VSCode 中使用像 [VSCode Tailwind](https://github.com/bradlc/vscode-tailwindcss) 这样的扩展有困难，开始调试的一个明智的地方是语言模式。

例如，对我来说，一个名为`application.html.erb`的文件将语言设置为`html.erb`，而不是预期的`erb`。这又导致 VSCode Tailwind 扩展被忽略，因为它没有为`html.erb`注册。

从长远来看，可能有一种方法可以让这个扩展查找任何 erb (*。erb -尽管这也可能有问题)。在短期内，我们可以告诉 VSCode 将任何`html.erb`文件映射回`erb`，如下所示:

```
"files.associations": {
    "*.html.erb": "erb"
  } 
```

Enter fullscreen mode Exit fullscreen mode

这一改变解决了我当前的问题，但它最终导致了另一个 bug。之前，我已经通过将`html.erb`映射到`html`为 Emmet 做了类似的修复。当然，由于文件类型被更改为`erb`，Emmet 需要以下更新:

```
 "emmet.includeLanguages": {
    "html.erb": "html",
    "erb": "html"
  } 
```

Enter fullscreen mode Exit fullscreen mode

配置覆盖的简易性既是一件好事也是一件坏事。很容易做出一个小小的改变，然后让它失控。但这比被束缚在某个无法工作的东西里(哪怕只是暂时的)要好得多。