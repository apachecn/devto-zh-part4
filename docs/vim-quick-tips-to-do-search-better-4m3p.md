# 在 vim 中搜索的快速提示

> 原文：<https://dev.to/iggredible/vim-quick-tips-to-do-search-better-4m3p>

能够快速搜索复杂的模式是开发人员不可或缺的技能。我想分享一些使用 Vim 的搜索来加快你的发展的技巧。如果使用得当，搜索可以成为 Vim 中一个强大的导航工具。

我希望你能学到一两件事！

[![happy fast typing](img/cb987c68406609e5b60959a87ed9725a.png)](https://i.giphy.com/media/Wsju5zAb5kcOfxJV9i/giphy.gif)

# 在 Vim 中搜索

您可以使用`/{your-search-pattern}`进行搜索。用`?{your-search-pattern}`倒退。要在一个文件中搜索所有的`foo`，按`/foo`。

# 查找下一个/上一个实例

一旦你输入你的搜索模式`/foo`，按下回车键(`<CR>`)就会进入我所说的“搜索模式”。你可以通过`n`找到“foo”的下一个实例，通过`N`找到“foo”的上一个实例。

关于`n`的一个超级方便的事情是，你可以在任何时候**做这件事**，甚至当你不再处于搜索模式时，通过输入`n`再次调用那个搜索。试试看。

我认为这是 vim 中一个强大的导航工具，因为您可以精确地快速上下移动文件。

# 开启 ignorecase 和 smartcase

我发现在不区分大小写的模式下搜索更容易。为此，在我的`vimrc`中我有`set ignorecase`。使用 ignorecase，键入`/foo`将匹配“福”、“福”、“福”等。

我还开着 smart case(`set smartcase`)。这两个通常[一起](https://stackoverflow.com/questions/2287440/how-to-do-case-insensitive-search-in-vim)。

ignore + smartcase combo 所做的是，只有当我们使用全部小写搜索时，它才会进行不区分大小写的搜索。如果我们使用至少一个大写字母，它将进行区分大小写的 T2 搜索。

例如:

```
foo
Foo
FoO 
```

Enter fullscreen mode Exit fullscreen mode

搜索`/foo`将匹配以上所有内容。搜索`/Foo`将只匹配“Foo”。搜索`/fOo`找不到匹配的。

前者不区分大小写，后两者区分大小写。有点不可思议，但我觉得这很直观。

# 禁用高亮显示

找到匹配后，有时我发现高亮显示的匹配会分散注意力。可以用`:noh`(无高亮)关闭。因为我经常使用这个，所以我将`:noh`映射到了`<esc><esc>`。

下面是我在`.vimrc` :
中的内容

```
nnoremap <esc><esc> :noh<return><esc> 
```

Enter fullscreen mode Exit fullscreen mode

# 自动完成搜索

Vim 足够智能，可以自动完成您的搜索关键字。当你在搜索“某物”时，你可以用`/somet<C-r><C-w>`给`/something`自动补全。

这并不是万无一失的，但我发现 90%的情况下它会给出我想说的那个词。

# 重复上次搜索

要重复上次搜索，您可以选择`/`或`//`。

# 查找词频

要查看一个文件中出现了多少个“foo ”,可以键入`:%s/foo//gn`。

它对整个文件(`%`)使用替换(`:s`)命令，但是我们取消了它(`n`)(我认为你可以只使用`:%s/{word}//n`而不用 g

我们可以把这个和`//`结合起来。如果我们*刚刚在*中搜索了 foo ( `/foo`)，并且我们想要在当前文件中找到多少个“foo”，我们可以做`:%s///gn`。

# 快速查找光标下的所有单词实例

在光标下查找单词有两种方法:

1.  要在单词边界内查找，请使用`*`或`#`
2.  要查找无单词边界，使用`g*`或`g#`。

一开始没弄清楚单词边界，我来解释一下:

```
[H]ello // [H] means cursor is on H
HelloWorld
Hello
Helloworld 
```

Enter fullscreen mode Exit fullscreen mode

如果我们使用`*`，我们将看到

```
[Hello] //highlights
HelloWorld
[Hello] //highlights
Helloworld 
```

Enter fullscreen mode Exit fullscreen mode

如果我们使用`g*`，我们将看到

```
[Hello] //highlights
[Hello]World //highlights
[Hello] //highlights
[Hello]world //highlights 
```

Enter fullscreen mode Exit fullscreen mode

使用`n`或`N`跳到下一个/上一个实例。

目前就这些。希望这对你们有所帮助。请随意分享其他搜索技巧——我很想听听你们的意见。

黑客快乐！！