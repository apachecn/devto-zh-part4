# 单手 Vim

> 原文：<https://dev.to/reergymerej/one-handed-vim-2hpa>

Vim 需要大量的技巧和练习。速度来自于时间和大量的肌肉记忆。这是一笔很大的投资，但值得。

然后你的手断了。

[![mono](img/afc3ab83d87dfed3c1efbae2df20d131.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PdmBHXeu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/szqmawsr4t9da62l7wik.jpg)

没关系。你仍然可以以合适的速度工作。这里有一些提示。

## 宏一切

### 基本

首先，添加一个宏来编辑您的 vimrc。你将需要编辑它很多。

```
" edit this file
:nnoremap <leader>ev :vsplit $MYVIMRC<cr>

" re-source this file
:nnoremap <leader>sv :source $MYVIMRC<cr> 
```

Enter fullscreen mode Exit fullscreen mode

今后，您可以立即打开、更改、保存和获取您的运行时配置。

为写入和退出添加映射。

```
:nmap <leader>ww :w<cr>
:nmap <leader>qq :wq<cr> 
```

Enter fullscreen mode Exit fullscreen mode

### 常见结构

当你写代码时，你会发现你经常使用的结构。为它们添加插入映射。例如，在插入模式下，如果我想`reduce`一个数组，我可以键入...

```
foo.rrr 
```

Enter fullscreen mode Exit fullscreen mode

它将与
交换

```
foo.reduce((acc, value) => {
  return {
  ...acc,
  }
}, {}) 
```

Enter fullscreen mode Exit fullscreen mode

花点时间写这些。你将不得不四处闲逛来找到你喜欢的，因此我们在开始时做了快速源的事情。这里有一些我用在 JavaScript 上的。

```
" insert if block
" if () {
"
" }
:imap iii if () {<cr>}<esc>kwa

" insert braces
" {
"
" }
:imap {{{ {<cr>}<esc>O

" insert brackets
" [
"
" ]
:imap [[[ [<cr>]<esc>O

" insert arrow fn
" () => {
"
" }
:imap ((( () => {{{return

" map loop
:imap .mmm .map((item) => {<cr>return item<cr>})<esc>kA

" reduce loop
:imap .rrr .reduce((acc, value) => {<cr>return {<cr>...acc,<cr>}<cr>}, {})<esc>kkA

" console
:imap ccc console.log()<esc>i 
```

Enter fullscreen mode Exit fullscreen mode

## 高效移动

花点时间想想你要导航到什么地方。懒`kkkkkjjjjkj`废话在单手模式下效果不好。看相对行号。适当地向他们移动。

*   用`17k`上去 17 行。
*   用`fr`转到下一个 r。
*   用`{`转到你上面的第一个空行。
*   用`3zj`向下 3 折。

稍微温习一下你的动作，花点时间把它们做正确。

大量使用`<C-o>`和`<C-i>`。当你按错键的时候，你会跳来跳去。这些带你向前或向后。

不要做一个纯粹主义者。有时候鼠标更快，尤其是当你感到困惑的时候。

## 避免打字

特别是如果你输入很长的实体名称，使用 [ctrlp.vim](https://github.com/ctrlpvim/ctrlp.vim) 或其他东西来自动完成。这避免了打字错误，并且可以更快。这包括打开文件时。

# 慢则顺，顺则快

这是步兵的座右铭，在这里很适用。我将在接下来的几周里练习有意识的移动和优化。是的，会很糟糕，但我会更快地从另一端出来。