# 使用 Python 修复 Python Markdown 代码块！

> 原文：<https://dev.to/rpalo/fixing-python-markdown-code-blocks-with-python-2mng>

*快速注意:这里的代码片段中的所有原始 markdown 代码块都用单引号而不是实际的反勾号显示，因为所有嵌套的反勾号都严重破坏了 Dev.to 的 markdown 渲染器。如果你是一个复制贴纸，你必须在 python 代码中把它们改回反斜杠。*

我正在尝试写作《尤利西斯》,到目前为止，我真的很喜欢它。但是昨天，我将我的一篇文章导出到 markdown，准备在我的博客上发表，我发现了一个小问题。Ulysses 用制表符而不是空格来格式化所有代码块。现在，我没有办法进入*那*整件事，但我至少会说 PEP8 告诉我们 Python 的标准是 4 个空格。如果我用我的 Python 代码发布带有——*颤栗*——标签的代码样本，我会是哪种有自尊的技术博客作者呢？

所以我问了他们！

> ![Ryan Palo profile image](img/e9e05f2e852d09ef0f88120663120f3f.png)瑞安帕洛@ paytastic![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)嘿 [@ulyssesapp](https://twitter.com/ulyssesapp) 我能在我的减价导出中控制制表符和空格吗？Python 代码片段似乎不应该用制表符导出。2019 年 7 月 02 日下午 14:12[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1146059025206743045)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1146059025206743045)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1146059025206743045)

他们反应很快，很有礼貌地告诉我“现在不行。”这很好。

我立即开始研究如何自己解决这个问题。我可以考虑定制降价格式吗？调整出口流程？但是，在我开始钻进那个兔子洞之前，我突然想到:这只是一条短信！

我是程序员！我的指尖掌握着宇宙的力量！(至少在我的机器上。)所以我开始为一个平台编写脚本，这个平台是为处理、修改和调整文本流而设计的:命令行。

## 痛击俏皮话

我的第一个想法是，这是一个真正简单的替换。我可以用一个简单的命令做到这一点！

```
$ sed $'s/\t/    /g' example.md
Hello this is text

'''python
def thing():
    print("Spaces!")
'''

There should be four spaces there. 
```

Enter fullscreen mode Exit fullscreen mode

老实说，这已经足够好了。但我当时正忙着，我想要更精细的控制。

## 用 Python 来做吧

我将向您展示整个事情，对于那些只是在这里复制/粘贴，然后我将通过重要的位和他们如何工作。本质上，我们遍历输入流的每一行，如果我们在 Python 代码块中并且有一个制表符，我们就用空格替换制表符。我从标准库中引入了`argparse`和`fileinput`,对用户体验进行了一些修饰。以下是我想到的:

```
#!/usr/bin/env python3 
import argparse
import fileinput

parser = argparse.ArgumentParser(description="Convert tabs in markdown files to spaces.")
parser.add_argument("-a", "--all", action="store_true", help="Convert all tabs to spaces")
parser.add_argument("-n", "--number", type=int, default=4, help="Number of spaces to use.")
parser.add_argument('files', metavar='FILE', nargs='*', help="files to read, if empty, stdin is used")

args = parser.parse_args()
if args.all:
    start_tag = "'''"
else:
    start_tag = "'''python"

in_code_block = False
for line in fileinput.input(files=args.files):
    if line.startswith(start_tag) and not in_code_block:
        in_code_block = True
    elif line.startswith("'''") and in_code_block:
        in_code_block = False
    elif in_code_block:
        line = line.expandtabs(args.number)

    print(line, end="") 
```

Enter fullscreen mode Exit fullscreen mode

商业逻辑的核心在这里:

```
in_code_block = False
for line in fileinput.input(files=args.files):
    if line.startswith(start_tag) and not in_code_block:
        in_code_block = True
    elif line.startswith("'''") and in_code_block:
        in_code_block = False
    elif in_code_block:
        line = line.expandtabs(args.number)

    print(line, end="") 
```

Enter fullscreen mode Exit fullscreen mode

循环遍历每一行，记录我们是否在一个代码块中(稍后会详细介绍)。如果我们在代码块中，请展开选项卡！最后，输出该行的新版本。

但是其他的东西是什么？即使在主代码中，也有对`fileinput`的引用。什么鬼东西？

### 使用文件输入

`fileinput`是 Python 标准库中一个简洁的(坦白地说被低估了)模块，它允许脚本从一个或多个文件参数甚至 STDIN 中加载输入，非常符合人体工程学。最常见的用例是在 it 的[文档中，它几乎短得可笑:](https://docs.python.org/3/library/fileinput.html) 

```
import fileinput

for line in fileinput.input():
    process(line) 
```

Enter fullscreen mode Exit fullscreen mode

有了这几行代码，您可以用任意多的文件名调用您的脚本，Python 会将它们的内容串成一个文本流。例如，如果您有一个脚本，它打印它接收到的所有文本的大写版本，称为`capitalize.py`，您可以这样运行它:

```
$ python3 capitalize.py README.md hello.txt banana.rb
# THIS IS THE TITLE OF MY README

CHECK OUT THE README CONTENTS.
SO MANY CONTENTS.
NOW HELLO.TXT IS HERE
YOOOOOOOO
DEF BANANA
    PUTS 'A MAN, A PLAN, CANAL BANANAMA'
END 
```

Enter fullscreen mode Exit fullscreen mode

但是 Ryan，在你的剧本里看起来不一样！你没有用同样的方式使用它！没错。我将它与另一个 CLI 电源模块相结合:

### 用 argparse 解析参数

是处理命令行参数、标志、选项和提供一点点用户界面的标准库方式。它的特殊语法是我必须经常查找的，但是它很轻量级，运行良好，并且做了我想做的事情。下面是相关代码。您将看到它是如何开始与上面的`fileinput`部分联系起来的。

```
parser = argparse.ArgumentParser(description="Convert tabs in markdown files to spaces.")
parser.add_argument("-a", "--all", action="store_true", help="Convert all tabs to spaces")
parser.add_argument("-n", "--number", type=int, default=4, help="Number of spaces to use.")
parser.add_argument('files', metavar='FILE', nargs='*', help="files to read, if empty, stdin is used")

args = parser.parse_args()
if args.all:
    start_tag = "'''"
else:
    start_tag = "'''python" 
```

Enter fullscreen mode Exit fullscreen mode

我们分三个阶段进行:

1.  首先我们创建`ArgumentParser`。它将为我们管理所有的解析。
2.  然后我们添加参数并指定它们的行为。在这种情况下，我添加了一个`--all`标志来创建它，这样我们就可以删除所有的制表符并恢复所有代码块的顺序，还添加了一个`--number`标志来告诉它每个制表符需要多少空格。如果我在 Ruby 或 JS 示例中喜欢两个空格，这可能会很有用。最后，我为用户想要提供的所有文件名添加了一个`*args`风格的位置参数。
3.  最后，既然一切都已指定，我们解析并处理参数。根据我们为每个输入指定的类型和动作，我们可以预期不同的行为。

最后一个小技巧是我们如何用这条小线
将`argparse`和`fileinput`联系在一起

```
for line in fileinput.input(files=args.files): 
```

Enter fullscreen mode Exit fullscreen mode

`fileinput.input`接受一个可选的文件名列表，而不是试图从传入的脚本参数中获取它们。因为`argparse`吞噬了所有的命令行参数，我们需要告诉它传递这些文件名，这样`fileinput`就可以完成它的任务。这一切都像一个魔咒！

## 标签是给世卫组织人把他们的玉米和土豆混合在一起的

没有，我对鸡毛蒜皮的事情没有强烈的看法，你问这个干嘛？无论如何，在尤利西斯的特写请求进入他们的队列之前，我已经得到了我的小剧本，这让我很高兴！你会问，它的效果如何？那么，你在这篇文章的代码示例中看到制表符或空格了吗？

*附:我知道你的命令行程序正潜伏在那里，迫不及待地向我抛出一个 awk 或 sed 字符串，它将完全按照我的要求来做，而我不必编写一行非常可读、可维护、非正则表达式的 Python 代码。😉我想看看！让他们飞吧！*