# 生成和增加数字的快速 vim 提示

> 原文：<https://dev.to/iggredible/quick-vim-tips-to-generate-and-increment-numbers-3849>

有时候，我需要在 vim 中快速增加或生成一列数字。Vim 8/ neovim 附带有用的数字技巧。

[![magician](img/23093d6f6c045ba42b3a01ec0cf7d59d.png)](https://i.giphy.com/media/KmIR3x7UG4cFy/giphy.gif)

我在这里分享其中的两个。

# 用 put 和 range 快速生成数字

您可以通过
快速生成升序数字

```
:put=range(1,5) 
```

Enter fullscreen mode Exit fullscreen mode

这会给你:

```
1
2
3
4
5 
```

Enter fullscreen mode Exit fullscreen mode

我们也可以控制增量。如果我们想快速生成降序数，我们做:

```
:put=range(10,0,-1) 
```

Enter fullscreen mode Exit fullscreen mode

其他一些变化:

```
:put=range(0,10,2) // increments by 2 from 0 to 10 
:put=range(5)      // start at 0, go up 5 times 
```

Enter fullscreen mode Exit fullscreen mode

这个技巧可能有助于在记笔记时生成一个列表。在 vim 中，显示当前行，我们可以使用`line('.')`。这可以与 put/range 结合使用。假设你现在在 40 号线上。要生成数字到第 50 行，你做:

```
:put=range(line(','),50) 
```

Enter fullscreen mode Exit fullscreen mode

你会得到:

```
40 // prints at line 41.
41
42
43
44
45
46
47
48
49
50 
```

Enter fullscreen mode Exit fullscreen mode

要调整上面的行号，可将其更改为`:put=range(line('.')+1,50)`以显示正确的行号。

# 快速递增一列数字

假设我们有一列数字，就像下面 HTML 中的 0:

```
<div class="test">0</div>
<div class="test">0</div>
<div class="test">0</div>
<div class="test">0</div>
<div class="test">0</div>
<div class="test">0</div>
<div class="test">0</div>
<div class="test">0</div>
<div class="test">0</div> 
```

Enter fullscreen mode Exit fullscreen mode

如果我们想增加所有的零(1，2，3，...)，我们可以很快做到这一点。以下是方法:

首先，将光标移动到顶部 0(我使用`[]`来表示光标的位置)。

```
<div class="test">[0]</div>
<div class="test">0</div>
<div class="test">0</div>
<div class="test">0</div>
<div class="test">0</div>
<div class="test">0</div>
<div class="test">0</div>
<div class="test">0</div>
<div class="test">0</div> 
```

Enter fullscreen mode Exit fullscreen mode

使用`VISUAL BLOCK`模式(`<C-v>`)，向下移动 8 次(`<C-v>8j`，直观选择所有 0。

```
<div class="test">[0]</div>
<div class="test">[0]</div>
<div class="test">[0]</div>
<div class="test">[0]</div>
<div class="test">[0]</div>
<div class="test">[0]</div>
<div class="test">[0]</div>
<div class="test">[0]</div>
<div class="test">[0]</div> 
```

Enter fullscreen mode Exit fullscreen mode

现在输入`g <C-a>`。瞧啊。

```
<div class="test">1</div>
<div class="test">2</div>
<div class="test">3</div>
<div class="test">4</div>
<div class="test">5</div>
<div class="test">6</div>
<div class="test">7</div>
<div class="test">8</div>
<div class="test">9</div> 
```

Enter fullscreen mode Exit fullscreen mode

[![shocked](img/9c0e9ec4695fc9ac8dfcaa5653c2456b.png)](https://i.giphy.com/media/Ki9ZNTNS7aC9q/giphy.gif)

等一等...刚刚发生了什么？

Vim 8 和 neovim 都有一个自动用`<C-a>`递增数字(用`<C-x>`递减)的特性。你可以去`:help CTRL-A`看看。

我们也可以通过在前面插入一个数字来改变增量。如果我们想用`10,20,30,...`代替`1,2,3,...`，那就用`10g<C-a>`来代替。

*顺便说一句，一个带`<C-a>`和`<C-x>`的超酷提示——你不仅可以增加数字，还可以增加八进制、十六进制、二进制和阿尔法数！对我来说，我不怎么用前三个，但我肯定经常用 alpha。字母是字母数字的别称。如果我们做`set nformats=alpha`，我们可以像做数字一样增加字母。*

很酷吧？请在下面的评论中与 Vim 分享任何其他数字技巧。感谢阅读！快乐游泳！