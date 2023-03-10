# 他们是如何在一条线上做到这些的？形的解决方案:删除重复的单词

> 原文：<https://dev.to/ekand/how-did-they-do-all-that-in-one-line-a-solution-for-the-kata-remove-duplicate-words-28n1>

我又做了一次:解决 CodeWars kata，然后惊叹于 CodeWars 社区投票选出的最佳实践的一行解决方案。

挑战是获取一串重复的单词，然后返回没有重复的单词，按照单词在输入字符串中第一次出现的顺序排列。

为了强调我看到它在一行中得到解决是多么印象深刻，下面是我的解决方案(顺便说一下，它与最佳实践投票数第二多的解决方案相同)。

```
def remove_duplicate_words(s):
    """ removes duplicate words from a string

    s:   a string of words separated by spaces
    """
    s = s.split()
    unique = []
    for word in s:
        if word not in unique:
            unique.append(word)
    s = ' '.join(unique)

    return s 
```

Enter fullscreen mode Exit fullscreen mode

下面是一行程序:

```
def remove_duplicate_words(s):
    return ' '.join(dict.fromkeys(s.split())) 
```

Enter fullscreen mode Exit fullscreen mode

注:我指的是一行函数体。

这是相当密集的，所以我要把它分解。我喜欢从内部开始。

```
s.split() 
```

Enter fullscreen mode Exit fullscreen mode

注意 s 是输入字符串。该表达式返回字符串中的单词列表。默认情况下。split 方法在空格字符处拆分字符串并丢弃空格。

进入下一步

```
dict.fromkeys(s.split()) 
```

Enter fullscreen mode Exit fullscreen mode

这一步很有意思。它用单词列表中的单词提供的关键字创建了一个字典(稍后将详细介绍为什么要创建字典)。

但是少了点什么。Python 中的字典不是应该有键*和*值吗？事实证明，当您使用 dict.fromkeys 形成一个参数中没有值的字典时，它只是为每个值输入 None。

为了澄清这一点，如果你把输入字符串 s = ' alpha beta beta gamma gamma delta alpha beta gamma gamma delta '以这种方式形成的字典打印出来，看起来是这样的。
下面是字典:
{'alpha ':无，' beta ':无，' gamma ':无，' delta ':无}

我们快到了。

```
' '.join(dict.fromkeys(s.split())) 
```

Enter fullscreen mode Exit fullscreen mode

注意这里的语法。它正在调用方法。用一个空格连接一个字符串。由于参数是一个字典，这将输出一个字符串，其中包含从键中提取的单词，用空格分隔。
(注:我不确定它为什么会忽略值。我测试过，即使其中一个键有值(比如 4)，它也会这样做。

剩下的就是返回这个表达式的值。注意，虽然这样做似乎更直观:

```
output = ' '.join(dict.fromkeys(s.split()))
return output 
```

Enter fullscreen mode Exit fullscreen mode

通过将表达式直接放在 return 之后，在一行中这样做是完全合法的，如一行解决方案:

```
return ' '.join(dict.fromkeys(s.split())) 
```

Enter fullscreen mode Exit fullscreen mode

这就是了。

进一步的注意:你可能想知道:为什么要用一个空键的字典呢？为什么不直接用一套。要理解这一点，你需要理解两件事。第一:虽然描述中没有明确说明这一点，但是这个例子清楚地表明，输出字符串中的单词需要按照它们在输入字符串中第一次出现的顺序排列。第二:集合是无序的，但是从 Python 3.6 开始，直到 Python 3.7，字典都是按照插入顺序排序的。所以现在应该清楚为什么字典是正确的选择了，尽管 set 似乎是更明显的答案。(而且，通过对挑战描述的严格阅读，你可能会认为这是一个正确的解决方案，但是 CodeWars 上的自动化测试功能不敢苟同。)

现在你知道了:一行代码就完成了我花了六个小时才完成的工作。虽然我的函数有更多的文档，所以我给自己打分。

顺便说一下，代码和这个描述的副本在我的 repo [这里](https://github.com/erik-kristofer-anderson/Codewars/tree/master/2019-08-21_remove_duplicate_words)。

期待给大家看下一部！