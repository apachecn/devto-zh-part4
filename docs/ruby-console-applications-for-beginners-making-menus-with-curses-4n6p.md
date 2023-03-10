# 面向初学者的 Ruby 控制台应用程序:用诅咒制作菜单

> 原文：<https://dev.to/nwdunlap17/ruby-console-applications-for-beginners-making-menus-with-curses-4n6p>

## 控制台应用程序和一些历史记录

控制台应用程序是程序员工具箱中最有用的部分。终端是最简单的用户界面，但它仍然足够灵活，可以作为大多数程序的有效前端。因为它不需要图形，您可以节省时间、金钱和空间！

当然，缺点是终端看起来和感觉起来就像实现起来一样简单。在任何地方导航都需要键入正确的输入并按回车键。而如果用户打错字了呢？他们必须把所有的东西都重新打一遍！即使使用简单的数字或“是/否”菜单也会感觉迟钝，因为每次输入后都需要按回车键。幸运的是，有一种方法可以让终端 UI 更光滑、更快，那就是诅咒。

[![No, not those kind of curses.](img/3cea6a36a275b8c3e66db3c8197f816b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YbWfBjPW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://media.tenor.cimg/76544063e9fe2270ab09f77e01d52e74/tenor.gif)

Curses 是一个库，可以让您轻松直接地控制终端屏幕。它最初是由 Ken Arnold 在 70 年代后期编写的，因为他发现传统的终端界面太局限于制作引人入胜的游戏。(有趣的事实是，他用诅咒构建的第一个游戏是 Rogue，它后来成为各地极客的文化试金石)。自 90 年代初以来，Curses 基本上没有变化，几乎可以在每一种现代编程语言中使用，所以它有一个非常强大的血统。

## 一件复杂的事情

这段简短的历史不仅仅是一些琐事，它对解释诅咒最奇怪的特征之一大有帮助。改变屏幕上的文本过去需要相当多的计算能力，如果需要更新多个文本部分，当计算机运行所有的改变时，可能会有相当大的延迟。Ken 对此的回答是创建一个缓冲区来存储屏幕应该是什么样子，然后一次更新整个屏幕。这种逻辑延续到今天的诅咒。**当你在诅咒中写东西时，它会更新缓冲区，不会显示在屏幕上**。你必须告诉屏幕更新，才能看到你的任何改变。虽然这听起来有点麻烦，但它保持这种方式四十年是有原因的。因为屏幕上的所有文本都是一次更新的，所以你的用户永远不会看到一个半成品框架。这意味着你的程序会看起来更干净，简单的动画可以通过快速改变帧来实现。

[![The screen will display whatever is in the buffer when refresh is called. And only that.](img/d784251eeb890992de68049a0646bef6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--P5BkrIj2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ek2wtz5j077sxn8vy77n.png)

## 这 6 个基本的咒骂命令

curses 被广泛使用的部分原因是因为它的简单语法。Curses 的所有功能都可以通过总共 6 个命令来访问。虽然还有更多的方法可以使用，但是这些方法都可以使用这 5 种方法进行复制。

*   **Curses.setpos** (int1，int 2)–设置光标的位置。第一个数字是行(从顶部的 0 开始)，第二个数字是进入行的字符数(从左侧的 0 开始)。
*   **curses . addstr**(string)–从当前光标位置开始将文本写入缓冲区。该空间中已经存在的任何文本都将被覆盖。
*   **curses . refresh**–在屏幕上显示当前缓冲区中的所有内容。
*   **curses . clear**–从缓冲区中删除所有内容。
*   **curses . getch**–等待用户按任意键，以字符串形式返回字符。
*   **Curses . init _ screen**–用于设置 Curses 将在终端的哪个区域运行。如果没有给定参数，则默认为整个终端。

**一个简单的应用**

[![Alt Text](img/a2137997eef350f31700bb70ff1cefb9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5BsF6kU0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1erszscrd34sm8v5tcm5.png)

首先，我们需要 curses，以便应用程序知道它可以访问 Curses 库。

我们加上字符串“你好，世界！”和“按任意键……”。注意，因为我们没有改变光标在这两个字符串之间的位置，所以第二个字符串从第一个字符串开始的地方开始。刷新命令只显示我们已经写入缓冲区的内容。

“这不会出现，直到你按下一个键”字符串被添加到缓冲区，但没有刷新跟随它，所以它还没有出现在屏幕上。一旦点击 Curses.getch 命令，程序就会停止并等待，直到按下一个键。

[![Alt Text](img/68a127a4f6f623256346529e280863d3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UPdyAzzM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8hkquzkxinu3muds2a6m.png)

按下“f”键会将其写入光标的当前位置(第一行的末尾)。它还会导致密钥变量变成字符串“f”。光标的位置更改为向下第 6 行，第 11 个字符，另一个字符串被写入缓冲区。然后刷新显示对缓冲区所做的所有更改。

[![Alt Text](img/c3ebdd79d50ea7b6c784abfbd45a1473.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6cvar3ac--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hy5so7vq0yxfyqqyqxpy.png)

最后一个 getch 在程序结束前捕获它。一旦程序结束，诅咒窗口关闭，所有显示的文本消失。

## 制作菜单

[![Alt Text](img/1c5f0a275eaaebb8e6d103d1c15e4184.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eZ-944wf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kt2oo13gs1av99mlgw54.jpg)

这个截图的左下部分显示了一个简单的例子，你可以用 curses 创建一个菜单。它将数组的元素显示为一个列表，用户可以浏览列表并选择一个条目。让我们分解呈现这个菜单的所有步骤。

1.  **构建我们的选项**——对于每个选项，我们需要一个显示给用户的字符串和一个返回的对象。

2.  **跟踪光标应该指向的地方**–我们使用一个名为“索引”的变量来存储箭头的当前位置

3.  **开始一个循环**–其余的逻辑发生在一个循环中，这将处理向用户显示菜单并对他们的输入做出反应。
    a. **列出选项**–对于列表中的每个选项，我们将其名称添加到缓冲区中的一行
    b. **放置箭头**–我们在由索引
    c. **绘制菜单**–我们使用 Curses.refresh 来显示我们构建的菜单
    d. **获取用户输入**–使用 Curses.getch，我们可以获取用户的输入。注意，我们总是必须在从用户那里得到输入之前更新屏幕，否则用户不会看到信息的当前状态。
    e. **解释输入**–我们检查用户是否想要更改索引或选择当前条目。如果用户想要进行选择，循环结束并返回选择值

在下面的例子中，为了清楚起见，菜单被分成了几种不同的方法。

*   **menu.start** 接受菜单的参数，调用其他方法来执行上述所有逻辑，然后最终返回被调用的所选值。(第一步)
*   **初始化**制作菜单的一个实例，并设置变量的默认值。(第二步)
*   **显示**处理所有的绘图和渲染。(步骤 3a-3c)
*   最后， **menu_loop** 处理循环和所有其他步骤 3 逻辑。

[![Alt Text](img/efe7ccb308788930cd1b3dd1f4ece443.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OzNSh3nY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/298s81uudhx4b4s1bk6t.png)

请注意，Curses.clear 在菜单循环期间从未被调用。这意味着当菜单打开时，屏幕上的任何内容都将保留在屏幕上。然而，这也意味着当我们希望文本消失时，我们必须手动覆盖它:这就是为什么我们在每一个选择前添加一些空白，以将箭头从其最后的位置移除。
还要注意，我们使用了一些额外的变量来检查列表的总大小。这样，当用户将索引移动到末尾时，我们可以轻松地循环索引。

## 为什么菜单很牛逼

让我们快速看一下它提供的优于文本解析菜单的一些优点。

*   **它很快**——文本解析菜单的一个问题是，它们要求用户对每个条目都按回车键。当他们连续做出多个选择时，这会大大降低速度。此外，该菜单只需要三个按钮:(向上、向下和选择)，而不必搜索选项的右键
*   **很直观**–在文本解析菜单中，用户可能不知道他们应该键入“蜘蛛侠”、“蜘蛛侠”还是“蜘蛛侠”。如果用户从菜单中选择，而不是输入名字，他们就不会犯这个错误。
*   它保护程序免于错误——因为菜单类只能返回给它的一个对象，你不需要验证用户输入是正确的。如果用户输入错误，只需要在 menu 类中处理错误，而不是每次程序要求输入时都要检查。
*   **它是通用的**–不管数组中的对象类型是什么，都可以使用这个菜单类。当我制作游戏时，我在游戏中使用了同样的菜单类来处理选择门、角色、技能、目标和装备。对于大多数选择，我只需要一行代码就可以调用菜单。

使用 curses，您可以改进控制台应用程序，使其菜单看起来和感觉起来更专业。如果你做得正确，它可以节省你大量的时间和精力来处理错误。

## 更咒骂

这篇文章只是对诅咒和它能做的一些事情做一个简短的介绍。请随意使用或改编本文中提供的任何代码，用于您自己的项目。如果你想更全面地了解 Curses，包括它非常有用的窗口命令，我推荐[这篇教程](https://stac47.github.io/ruby/curses/tutorial/2014/01/21/ruby-and-curses-tutorial.html)。