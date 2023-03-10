# 带有 Ruby 诅咒的可滚动菜单

> 原文：<https://dev.to/aspenjames/scroll-able-menus-with-curses-in-ruby-573g>

这是我开发基于终端的看板
板应用程序的博客系列的第 2 部分。要阅读第一部分，请点击这里的。我想谈谈我为此需要的一个
特性，以及我如何用 Ruby 实现它，那就是
拥有可滚动的菜单选项。

## 目标

我希望有一种方式向我的用户显示选项菜单，他们可以通过滚动(用键盘)来
导航，并使用
回车键进行选择。我最初期望有一个方法或类来为我执行这个
功能，因为它看起来像是一个如此常见的用例，但那不是
的情况！结果是我们需要更多的手动操作。让我们从一个
的基本例子开始:

## 简单的滚动菜单

我在这里做了一个非常基本的可滚动菜单的例子。这个程序所做的就是将一组条目显示为一个列表，并允许用户滚动浏览。我定义了一个常量`OPTIONS`，它是一个
字符串数组——这些将是我们的列表项。我们还有一个
实例变量`@highlight`，它将作为一个*指针*指向当前“选中”项的
**索引**，在`0`初始化。您还会找到名为`move_highlight_up`和`move_highlight_down`的方法。这些手柄
上下移动高亮显示的项目。当前书写的方式，
到达时，选择从下到上和从上到下环绕。
换句话说，如果你在
列表的底部试图向下滚动，那么选择会绕到顶部。让我们来看看
中的一个，看看它是如何工作的:

```
def move_highlight_down
  if @highlight == OPTIONS.length - 1
    @highlight = 0
  else
    @highlight += 1
  end
end 
```

因为我们正在列表中向下移动**，我们想要首先检查我们是否在底部
。集合中最后一项的索引将比它的
长度小 1，所以这就是为什么我们要从`OPTIONS.length`中减去 1。如果是这种情况，我们想回到第一项，索引 0。如果我们不在
底部，我们增加高亮指针。我们反过来做同样的事情，让
上移高光。如果我们希望用户能够滚动到列表的末尾并停止，而不是换行，我们可以这样写这个方法:** 

```
def move_highlight_down
  unless @highlight >= OPTIONS.length - 1
    @highlight += 1
  end
end 
```

在这个例子中，如果我们还没有到达列表的底部，我们将增加高亮指针。有几个其他的方法来实现
这一点(就像任何 Ruby 一样)，但是这应该给你几个开始
的选项。

### 显示菜单

我们有一个名为`display_menu`的方法，它实际上处理在用户屏幕上绘制菜单
。这就是“选择”一个项目
的视觉元素出现的地方。我们将使用一些[属性](http://tldp.org/HOWTO/NCURSES-Programming-HOWTO/attrib.html)，以使我们选择的
项目在视觉上不同于其他项目。我们感兴趣的属性
是`A_STANDOUT`,因为它使文本变得更好...出众。当一个条目的索引匹配我们的高亮指针时，我们想要应用
这个属性。那个代码
看起来是这样的:

```
def display_menu
  current_line = 1

  OPTIONS.each_with_index do |opt, idx|
    WINDOW.setpos(current_line, 1)
    if @highlight == idx
      WINDOW.attron(Curses::A_STANDOUT)
      WINDOW.addstr(opt)
      WINDOW.attroff(Curses::A_STANDOUT)
    else
      WINDOW.addstr(opt)
    end
    current_line += 1
  end

end 
```

看起来这里发生了很多事情，但是一旦我们
把它分解开来，事情就没那么复杂了。首先，我们希望初始化一个变量来保存我们当前的行
位置。我们迭代所有的列表项(这里是`OPTIONS`)。对于每个
迭代，我们设置光标位置(使用我们的`current_line`
变量)并使用`WINDOW.addstr`显示该选项。然而，我们也首先检查该选项的索引是否与我们的高亮指针相匹配。如果
为真，那么我们将应用`A_STANDOUT`属性。我们也不想忘记在每次迭代中增加我们的 T4！

### 让用户滚动

现在我们有了显示菜单和移动
高亮的好的助手方法，我们需要一种让用户与菜单交互的方法。我
将专注于键盘导航，但 Curses 也支持[鼠标
集成](http://tldp.org/HOWTO/NCURSES-Programming-HOWTO/mouse.html)。我的应用程序没有鼠标支持(可能是一个
拉伸目标)，所以我们现在跳过它。我制作这些
滚动菜单的方法是用一个基本的`loop`像这样:

```
loop do
  display_menu
  Curses.refresh
  ch = WINDOW.getch

  case ch
  when 'j'
    move_highlight_down
  when 'k'
    move_highlight_up
  when 'q'
    break
  end

end 
```

循环的前三行调用我们的`display_menu`方法，刷新
屏幕，并等待用户按键。我们将哪个键被按下的
存储在一个名为`ch`的变量中，并对其运行一个`case`语句。在这个基本示例中，我们的控制字符是‘j’、‘k’和‘q’。您定义为“退出”字符的任何控制字符
将`break`循环并允许程序
继续。现在它唯一做的另一件事是关闭，所以在这里按“q”
退出程序。按‘j’调用我们的`move_highlight_down`方法，按‘k’
调用我们的`move_highlight_up`方法。

## 做出选择

我们现在有了一个用户可以滚动浏览的菜单，但它本身并不是非常有用。真正有用的是当我们可以使用
滚动菜单让我们的用户从菜单中选择
来执行一个动作。我们上面的选项被构造成一个数组，
我们的`@highlight`指针是对当前被选中的
项的索引的引用。这是故意这样构造的，因为
意味着我们可以使用指针从数组中返回一个特定的元素！
让我们看看这段代码是什么样子的:

```
def get_choice
  loop do
    display_menu
    Curses.refresh
    ch = WINDOW.getch

    case ch
    when 'j'
      move_highlight_down
    when 'k'
      move_highlight_up
    when 10 # '10' is the key code for 'Enter'
      return OPTIONS[@highlight]
    end
  end
end 
```

主要的区别(除了放入一个方法中)是我们使用 enter
键作为控制字符来定义选择，并且我们已经删除了用‘q’键退出的
选项。这是可选的，我们可以把退出
选项留在那里，作为快速退出程序的一种方式，完全取决于你想要如何定义接口。重要的是，当用户按下回车键时，我们将
返回当前高亮显示的选项。

## 更复杂的菜单

有一个基本的菜单很好，但是对于我的用例来说，我需要一种方法让用户能够垂直和水平地滚动选项。我正在使用的接口
有三个项目集合，排列在三列中，所以一个基本的指针
看起来不像是做这件事的理想方式。在重新考虑哪种数据结构
更合适的时候，我把它缩小到了我想要的几个关键特性——一种能够保存两个整数的
数据结构，并让我以一种让它们的用途更清晰的方式
来访问它们。为此，我求助于一个[结构](https://ruby-doc.org/core-2.6.3/Struct.html)。一个
结构可以让我以一种语义上有意义的方式访问信息，同时减少创建整个类的开销。它看起来像这样一个
小东西:

```
@highlight = Struct.new(:col, :itm).new(1, 1) 
```

与之前简单的高亮指针相比，这样做的好处是我们现在可以保存对列号和行号的引用。我们访问它们的方式也描述了它们的值- `@highlight.col`访问选中的列，`@highlight.itm`获取集合中该项的索引。您可以在`curses-playground`分支下的【tknbn 资源库】【tknbn】中看到这段代码。在`lib/tknbn/curses/main_menu.rb`文件中，我们有一个正在处理的`Project`对象。该对象在三个不同的阶段有许多`Items`——TODO、In Progress 和 Done。上下移动高亮显示的方法基本相同，只是增加了一个`get_num_items`方法，该方法返回当前所选列的集合长度。我们左右移动的方法是这样的:

```
def move_cursor_right
  if @hl.col == 3 # If rightmost selected
    @hl.col = 1   # Move to leftmost
    num_items = get_num_items
    if @hl.itm > num_items
      @hl.itm = num_items
    end
  elsif @hl.col == 1 # If leftmost selected
    @hl.col = 2      # Move to middle
    num_items = get_num_items
    if @hl.itm > num_items
      @hl.itm = num_items
    end
  else
    @hl.col = 3 # Move rightmost
    num_items = get_num_items
    if @hl.itm > num_items
      @hl.itm = num_items
    end
  end
end

def move_cursor_left
  if @hl.col == 1 # If leftmost selected
    @hl.col = 3   # Move to rightmost
    num_items = get_num_items
    if @hl.itm > num_items
      @hl.itm = num_items
    end
  elsif @hl.col == 3 # If rightmost selected
    @hl.col = 2      # Move to middle
    num_items = get_num_items
    if @hl.itm > num_items
      @hl.itm = num_items
    end
  else
    @hl.col = 1 # Move leftmost
    num_items = get_num_items
    if @hl.itm > num_items
      @hl.itm = num_items
    end
  end
end 
```

如果你注意到重复的代码，你就对了！有机会
在那里重构一些片段。我们试图用行
`@hl.itm = num_items`完成的是一种处理调整我们
所指向的项目索引的方法，以防它超出了`@hl.col`
现在引用的集合的范围。如果我们选择了列的第 5 个元素
，并且我们移动到一个少于 5 个元素的列，就会发生这种情况。每当我们移动到一个
新列时，我们希望确保我们指向一个有效的元素。我正在开发的另一个方法叫做`adjust_highlight`，它做了很多
相同的事情，但是也可以删除
集合中的最后一个项目，等等。我不写很多递归方法/函数，所以它可能会被优化，但我认为它很酷。

## 接下来是什么

我开始觉得我已经搞清楚了这个项目的大部分内容，所以是时候进行一轮重大的重构和清理代码了。我有意让自己到处写代码
，同时我学习了一些使用这个库的最佳方法，我的想法是
一旦我
适应了某个地方，就会有一个相当大的重构回到主分支。接下来我想探索一下[表单](http://tldp.org/HOWTO/NCURSES-Programming-HOWTO/forms.html)，在重构之前，我可能会
以同样的方式来做这件事。下一期博客文章
很可能是关于主要的重构本身。**