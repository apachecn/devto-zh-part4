# 机械化的超现实主义:看《荒谬的光泽》

> 原文：<https://dev.to/valeriecodes/mechanizing-surrealism-a-look-at-absurdgloss-4hen>

最近，我花了一些时间探索编程的陌生角落。我一直在玩 Python 中的自然语言处理工具，上周我写了关于[在爱丽丝梦游仙境](https://dev.to/valeriecodes/exploring-natural-language-processing-with-alice-in-wonderland-ldc)中找到独特的单词。

当我解决这个问题时，我在 [Nodebox 英语语言库](https://www.nodebox.net/code/index.php/Linguistics)的文档中发现了一个神秘的方法。它被称为`absurd_gloss`，并声称要返回“对这个词的荒谬描述”

不太确定这是什么意思，我自己尝试了一下:

```
>>> en.noun.absurd_gloss('castle')
'a place where you are just as comfortable and content as if you were home'
>>> en.noun.absurd_gloss('aardvark')
'primitive reptile having no opening in the temporal region of the skull; all extinct except turtles'
>>> en.noun.absurd_gloss('truck')
'handcart for moving a load of laundry'
>>> en.noun.absurd_gloss('computer')
'a subsidiary organ of government created for a special purpose; "are the judicial instrumentalities of local governments adequate?"; "he studied the French instrumentalities for law enforcement"'
>>> en.noun.absurd_gloss('cow')
'sure-footed mammal of mountainous northwestern North America'
>>> en.noun.absurd_gloss('pelican')
'chiefly web-footed swimming birds'
>>> en.noun.absurd_gloss('sleep')
'the London residence of the British sovereign' 
```

我发现结果有点不真实。以一种不可思议的方式与原始输入有所关联，有时还会偏离基础。这里发生了什么？

我翻了翻源代码，找到了方法定义:

```
def absurd_gloss(q, sense=0, pos=NOUNS, up=3, down=2):

    """

    Attempts to simulate humor:
    takes an abstract interpretation of the word,
    and takes random examples of that abstract;
    one of these is to be the description of the word.

    The returned gloss is thus not purely random,
    it is still faintly related to the given word.

    """

    from random import random, choice

    def _up(path):
        p = hypernym(path, sense, pos)
        if p: return p[0][0]
        return path

    def _down(path):
        p = hyponym(path, sense, pos)
        if p: return choice(p)[0]
        return path

    for i in range(up): q = _up(q)
    for i in range(down): q = _down(q)
    return gloss(q) 
```

好吧，让我们打开这个。首先，评论“试图模拟幽默。”😂教计算机变得有趣可能仅仅是因为缓存和命名困难的问题。

`q`是输入单词，`sense`指定使用单词的哪个含义，从最常用到最不常用(默认为最常用)。`pos`是要使用的词性(默认为名词)。然后还有`up`和`down`的号码。

`abstract_gloss`的工作方式是获取输入单词的第一个上位词，然后是上位词的第一个上位词，依此类推。

然而，上位词是什么呢？

问得好。文档将其描述为“抽象”，但是给出例子可能最有帮助:

```
>>> en.noun.hypernym('cloud')
[['physical phenomenon']]
>>> en.noun.hypernym('animal')
[['organism', 'being']]
>>> en.noun.hypernym('person')
[['organism', 'being'], ['causal agent', 'cause', 'causal agency']]
>>> en.noun.hypernym('castle')
[['mansion', 'mansion house', 'manse', 'hall', 'residence']] 
```

因此，它连续地取那些在`up`参数中指定的次数(默认为 3)，然后取随机的下位词在`down`参数中指定的次数。

什么是下位词？

下义词是给定单词的一个例子。让我们来看看更多的例子(为简洁起见，返回值被截断):

```
>>> en.noun.hyponym('cloud')
[['coma'], ['nebula'], ['aerosol'], ['cosmic dust'], ['dust cloud'], ['mushroom', 'mushroom cloud', 'mushroom-shaped cloud']]
>>> en.noun.hyponym('animal')
[['pest'], ['critter'], ['creepy-crawly'], ['darter']]
>>> en.noun.hyponym('person')
[['self'], ['adult', 'grownup'], ['adventurer', 'venturer']]
>>> en.noun.hyponym('castle')
[['Buckingham Palace']] 
```

`absurd_gloss`获取连续的上义词，然后获取连续的下义词，然后返回最后一个下义词的注释(或定义)。因为下位词是随机选择的，所以相同的输入可能会得到不同的结果。你开始使用的单词越通用(因此有越多的下位词)，结果偏离轨道的可能性就越大。

我添加了一些打印语句来跟踪整个过程:

```
>>> en.noun.absurd_gloss('clown')
Hypernym : clown
Hypernym : fool
Hypernym : simpleton
Hyponym : person
Hyponym : perspirer
Resulting word: perspirer
'a person who perspires'
>>> en.noun.absurd_gloss('clown')
Hypernym : clown
Hypernym : fool
Hypernym : simpleton
Hyponym : person
Hyponym : watcher
Resulting word: browser
'a viewer who looks around casually without seeking anything in particular'
>>> en.noun.absurd_gloss('pelican')
Hypernym : pelican
Hypernym : pelecaniform seabird
Hypernym : seabird
Hyponym : aquatic bird
Hyponym : seabird
Resulting word: gaviiform seabird
'seabirds of the order Gaviiformes'
>>> en.noun.absurd_gloss('person')
Hypernym : person
Hypernym : organism
Hypernym : living thing
Hyponym : object
Hyponym : part
Resulting word: language unit
'one of the natural units into which linguistic messages can be analyzed'
>>> en.noun.absurd_gloss('universe')
Hypernym : universe
Hypernym : natural object
Hypernym : whole
Hyponym : concept
Hyponym : conceptualization
Resulting word: approach
'ideas or actions intended to deal with a problem or situation; "his approach to every problem is to draw up a list of pros and cons"; "an attack on inflation"; "his plan of attack was misguided"'
>>> en.noun.absurd_gloss('animal')
Hypernym : animal
Hypernym : organism
Hypernym : living thing
Hyponym : object
Hyponym : je ne sais quoi
Resulting word: je ne sais quoi
'something indescribable' 
```

当然，通过调整`up`和`down`的值可以做更多有趣的工作，使结果更抽象或更抽象，但我希望这是对一种起初看起来完全没有意义的方法的有趣观察。🔮🦄

这只是在 [Nodebox 英语语言库](https://www.nodebox.net/code/index.php/Linguistics)中可用的许多有趣工具之一，如果你觉得这很有趣，那就值得进一步探索。