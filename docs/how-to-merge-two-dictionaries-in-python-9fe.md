# 如何在 Python 中合并两个字典

> 原文：<https://dev.to/renegadecoder94/how-to-merge-two-dictionaries-in-python-9fe>

当我试图为这个系列寻找一个主题时，我要么决定写一些我刚刚学到的东西，要么我选择写一些我从栈溢出的顶级 Python 问题[列表中找到的东西。今天，我将通过讲述如何在 Python 中合并两个字典来解决这两个问题。](https://stackoverflow.com/questions/tagged/python?sort=votes&pageSize=50)

## 问题介绍

在本系列的早些时候，我提到了一个类似的问题，我想将两个列表转换成一个字典。在那篇文章中，我介绍了将一个列表映射到另一个列表的各种方法。这一次我想把两个字典转换成一个字典，就像这样:

```
yusuke_power = {"Yusuke Urameshi": "Spirit Gun"}
hiei_power = {"Hiei": "Jagan Eye"}

# Insert merge code here 
powers = { "Yusuke Urameshi": "Spirit Gun", "Hiei": "Jagan Eye"} 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们有两个字典:`yusuke_power`和`hiei_power`。每本字典都把[一个鱼雨哈卡绍人物](https://myanimelist.net/anime/392/Yuu%E2%98%86Yuu%E2%98%86Hakusho)映射到他们的一个能力上。在这种情况下，我选择了 Yusuke 和他的精神枪以及 Hiei 和他的 Jagan Eye。最终，我们希望能够合并这些字典，这样我们就有了一个字符及其能力的集合。下面让我们看看我们能否做到这一点。

## 方案

和往常一样，我喜欢列出一些可能的方法来解决这个问题。首先，我们将尝试一个强力解决方案，然后我们将深入一些更复杂的解决方案。

### 用蛮力合并两个字典

作为本系列的传统，我总是喜欢以自己的解决方案开始。在这种情况下，我们希望迭代一个字典，并将它的条目添加到另一个字典:

```
yusuke_power = {"Yusuke Urameshi": "Spirit Gun"}
hiei_power = {"Hiei": "Jagan Eye"}

for key, value in hiei_power.items(): 
    yusuke_power[key] = value 
```

Enter fullscreen mode Exit fullscreen mode

当然，这个解决方案还有很多不尽如人意的地方，但是它完成了任务。最终，`yusuke_power`应该看起来像我们想要的`powers`字典。

为了完成更接近我们想要的东西，我们必须分别迭代两个字典:

```
yusuke_power = {"Yusuke Urameshi": "Spirit Gun"}
hiei_power = {"Hiei": "Jagan Eye"}

powers = dict()
for dictionary in (yusuke_power, hiei_power): 
    for key, value in dictionary.items(): 
        powers[key] = value 
```

Enter fullscreen mode Exit fullscreen mode

不幸的是，这个解决方案不能很好地扩展。也就是说，有更好的方法来解决这个问题。

### 将两本词典合并成一本词典理解

由于我是理解的忠实粉丝，我认为值得一提的是上面的解决方案可以用字典理解写成一行:

```
yusuke_power = {"Yusuke Urameshi": "Spirit Gun"}
hiei_power = {"Hiei": "Jagan Eye"}

powers = {key: value for d in (yusuke_power, hiei_power) for key, value in d.items()} 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们编写了一个字典理解，它遍历两个字典，并将每个条目复制到一个新字典中。很自然，它的工作方式就像暴力解决方案一样。

### 用复制和更新合并两个字典

与 Python 中的许多集合一样，它们有一个与之关联的内置复制函数。因此，我们可以利用复制功能来生成一个新的字典，其中包含原始字典的所有条目。此外，字典具有更新功能，可以将一个字典中的所有条目添加到另一个字典中:

```
yusuke_power = {"Yusuke Urameshi": "Spirit Gun"}
hiei_power = {"Hiei": "Jagan Eye"}

powers = yusuke_power.copy()
powers.update(hiei_power) 
```

Enter fullscreen mode Exit fullscreen mode

使用这个解决方案，我们能够生成包含最初两个字典中所有条目的`powers`字典。一个额外的好处是，`copy`和`update`是向后兼容的，所以 Python 2 用户不会觉得被忽略了。

值得注意的是，我们可以扩展这个解决方案，用一个自定义函数来合并任意数量的字典:

```
def merge_dicts(*dicts: dict): 
  merged_dict = dict() 
  for dictionary in dicts: 
    merge_dict.update(dictionary) 
  return merged_dict 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以生成一个新的字典，它包含任意数量的字典中的所有条目。

### 用字典解包合并两个字典

当 Python 3.5 推出时，它引入了一个字典解包语法，允许我们用一个新的操作符来合并字典:

```
yusuke_power = {"Yusuke Urameshi": "Spirit Gun"}
hiei_power = {"Hiei": "Jagan Eye"}

powers = {**yusuke_power, **hiei_power} 
```

Enter fullscreen mode Exit fullscreen mode

自然地，这个解决方案适用于任意数量的参数:

```
yusuke_power = {"Yusuke Urameshi": "Spirit Gun"}
hiei_power = {"Hiei": "Jagan Eye"}

powers = {**yusuke_power, **hiei_power, "Yoko Kurama": "Rose Whip"} 
```

Enter fullscreen mode Exit fullscreen mode

当然，缺点是向后兼容。如果您仍然在使用 Python 2 甚至更老版本的 Python 3，这个特性可能对您不可用。无论如何，我认为这是一个非常聪明的语法，我喜欢它的外观。

## 表现

在本系列中，我第一次认为看一看上面每种方法的性能是有益的(如果您幸运的话，我可能会更新旧文章以包括性能)。为此，我将使用内置的`timeit`库。

要使用`timeit`库，我们必须设置一些测试字符串:

```
setup = """
yusuke_power = {"Yusuke Urameshi": "Spirit Gun"}
hiei_power = {"Hiei": "Jagan Eye"}
powers = dict()
"""

brute_force = """
for dictionary in (yusuke_power, hiei_power): 
  for key, value in dictionary.items(): 
    powers[key] = value
"""

dict_comprehension = """
powers = {key: value for d in (yusuke_power, hiei_power) for key, value in d.items()}
"""

copy_and_update = """
powers = yusuke_power.copy()
powers.update(hiei_power)
"""

dict_unpacking = """
powers = {**yusuke_power, **hiei_power}
""" 
```

Enter fullscreen mode Exit fullscreen mode

有了我们的字符串设置，我们可以开始我们的性能测试:

```
>>> import timeit
>>> timeit.timeit(stmt=brute_force, setup=setup)
1.517404469999974
>>> timeit.timeit(stmt=dict_comprehension, setup=setup)
1.6243454339999062
>>> timeit.timeit(stmt=copy_and_update, setup=setup)
0.7273476979999032
>>> timeit.timeit(stmt=dict_unpacking, setup=setup)
0.2897768919999635 
```

Enter fullscreen mode Exit fullscreen mode

事实证明，字典解包非常快。作为参考，我在 Surface Go 上用 Windows 10 和 Python 3.7.1 进行了测试。

## 稍微重述一下

嗯，这是我所有的典型解决方案。尽管如此，请注意所有这些解决方案都将覆盖重复的值。换句话说，如果两个字典包含相同的键，最后一个要合并的字典将覆盖前一个字典的值。

此外，值得注意的是，所有这些解决方案都执行了字典的浅层拷贝。因此，可能是嵌套或存储对象的字典只会复制它们的引用，而不会复制实际值。如果这是应用程序中的一个约束，您可能需要编写自己的递归复制函数。

无论如何，以下是所有的解决方案:

```
yusuke_power = {"Yusuke Urameshi": "Spirit Gun"}
hiei_power = {"Hiei": "Jagan Eye"}
powers = dict()

# Brute force for dictionary in (yusuke_power, hiei_power): 
  for key, value in dictionary.items(): 
    powers[key] = value

# Dictionary Comprehension powers = {key: value for d in (yusuke_power, hiei_power) for key, value in d.items()}

# Copy and update powers = yusuke_power.copy()
powers.update(hiei_power)

# Dictionary unpacking (Python 3.5+) powers = {**yusuke_power, **hiei_power}

# Backwards compatible function for any number of dicts def merge_dicts(*dicts: dict): 
  merged_dict = dict() 
  for dictionary in dicts: 
    merge_dict.update(dictionary) 
  return merged_dict 
```

Enter fullscreen mode Exit fullscreen mode

而且，就是这样！一如既往，我感谢你的支持。如果你喜欢这篇文章，帮我一个忙，并与他人分享。对于那些感觉特别慷慨的人，[可以考虑成为叛离编码者](https://therenegadecoder.com/members/)的一员。如果你不相信，可以看看其他 Python 文章:

[![renegadecoder94 image](img/4ad5302c8e9c3fbd47325dc4154af595.png)](/renegadecoder94) [## 使用模运算的石头剪刀布

### 杰里米·格里夫斯基 3 月 18 日 19 时 11 分阅读

#python #beginners #tutorial #programming](/renegadecoder94/rock-paper-scissors-using-modular-arithmetic-2j4p)[![renegadecoder94 image](img/4ad5302c8e9c3fbd47325dc4154af595.png)](/renegadecoder94) [## 如何用 Python 写列表理解

### 杰里米·格里夫斯基 1919 年 5 月 3 日 6 分钟阅读

#python #beginners #tutorial #programming](/renegadecoder94/how-to-write-a-list-comprehension-in-python-n58)[![renegadecoder94 image](img/4ad5302c8e9c3fbd47325dc4154af595.png)](/renegadecoder94) [## 最酷的编程语言特性

### 杰里米格里夫斯基 4 月 3 日 1913 分钟阅读

#programming #computerscience #coding #learning](/renegadecoder94/the-coolest-programming-language-features-1953)

再次感谢支持！在你离开之前，分享你对你想在评论中看到的主题的建议。

帖子[如何在 Python 中合并两个字典](https://therenegadecoder.com/code/how-to-merge-two-dictionaries-in-python/)首先出现在[这个叛逆的编码者](https://therenegadecoder.com)上。