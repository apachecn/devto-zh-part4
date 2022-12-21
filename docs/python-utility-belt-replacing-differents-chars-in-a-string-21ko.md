# Python 实用工具带:替换字符串中的不同字符。

> 原文：<https://dev.to/furtleo/python-utility-belt-replacing-differents-chars-in-a-string-21ko>

嗨 Devs，
[![oi](img/8ff85f3654936d633401137ee73e3ada.png)](https://i.giphy.com/media/ASd0Ukj0y3qMM/giphy.gif) 
这是我在 dev.to 上的第一篇文章，我决定谈谈 Python，我的主要编程语言，并创建一个名为“Python 实用工具带”的系列，它基本上将由解决开发日常见问题的小策略组成。
[![oi](img/7583fa9b4327d58ad880506fbee1b43f.png)](https://i.giphy.com/media/7IfFDmSbfrztS/giphy.gif) 
在本帖中我们将谈论字符替换。

所以，让我们想象一个简单的例子...您有以下字符串:

```
music = "Stairway_to_heaven-Led_Zeppelin.mp3" 
```

Enter fullscreen mode Exit fullscreen mode

你需要用空格替换“_”，在 Python 中很简单，你只需要运行:

```
music.replace('_',' ') 
```

Enter fullscreen mode Exit fullscreen mode

您将收到:

```
Stairway to heaven-Led Zeppelin.mp3 
```

Enter fullscreen mode Exit fullscreen mode

很简单，不是吗？！但是，如果你想用空格代替`_`、`-`和`.`呢？？？
python 初学者可以想象这样的代码:

```
music.replace('_',' ').replace('-',' ').replace('.',' ') 
```

Enter fullscreen mode Exit fullscreen mode

[![oi](img/171f37d73dd2f599c07a10a2a62f13cb.png)](https://i.giphy.com/media/6RCWzHNG5mP0k/giphy.gif)T5【耶...而且是 stackoverflow 里常见的回答。上面的代码有用吗？是的，它可以工作，但是有另一种方法可以解决同样的问题:使用 re lib。你只需要在你的代码中导入`re`并使用`sub`函数:

```
import re

music = "Stairway_to_heaven-Led_Zeppelin.mp3"

print(re.sub('_|-|\.', ' ', music)) 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我们调用了接收三个参数的 sub 函数:

```
re.sub(char that will be replaced,new char,string) 
```

Enter fullscreen mode Exit fullscreen mode

为了使用第一个参数替换多个字符，我们使用了`regex`，但我们不会深入研究它，不。基本上每个需要替换的字符都由`|`分隔，所以如果你只需要替换`_`和`-`，你可以运行`re.sub('_|-', ' ', music)`。注意`.`伴随着`\`，那是因为在 Regex 中，`.`有另外一个功能，所以我们需要用`\`来指定一个点的使用。

我将在这里完成这篇文章。如果你在帖子中发现任何错误(英语，编程，语法)，请评论。
这有助于我提高。

更多请关注我，再见。
[![falow](img/7813d69bc09f75bcac67d37025326dcb.png)T3】](https://i.giphy.com/media/1AjCJU2gLaDtelBisz/giphy.gif)