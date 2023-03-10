# 用二进制解决蛋奶酒问题

> 原文：<https://dev.to/rpalo/solving-eggnog-problems-with-binary-4fcp>

二进制。这感觉就像一个概念，你只需要深奥或复杂的事情，如复数问题，编写字节码，建立可执行文件，加密和数字取证。但是真的，你可以用电灯开关做的只是数学！有时候，知道灯的开关是开还是关，是对还是错，是进还是出，实际上比其他看待问题的方式更容易推理。

让我们来看看我在休息时间摆弄的代码难题的出现。

今年是 2015 年。今天是 17 号。精灵也买了。很多。蛋奶酒。150 升太多了！幸运的是，你有一堆容器可以放它。不太幸运的是，所有的容器都是不同的和不可预测的大小。这就是你的谜题输入。看起来是这样的:

```
11
30
47
31
32
36
3
1
5
3
32
36
15
11
46
26
28
1
19
3 
```

Enter fullscreen mode Exit fullscreen mode

喔，对吧？所以问题是这样的:有多少种不同的方法，你可以用这些容器的不同组合(每一个都以升为单位)来精确地容纳*150 升*？

## 最初的想法

我的第一个想法是我们在处理组合。我们不关心容器的使用顺序，我们只想尝试使用或不使用每个容器的每种可能性。“嚯！Python 有个`itertools.combinations`函数会帮我！”但是后来我查了一下，它只返回一个更大集合的固定长度子集组合。固定长度对我没有帮助！我的结果可能是任意数量的容器。

像往常一样，我的第二个想法是，“好吧，让我们用最简单的方法。我要循环什么？”但是你会循环什么呢？你必须遍历所有东西，找到长度为 1 的所有组合，然后是长度为 2 的所有组合，然后是长度为 3 的所有组合…实际上，你知道吗？当我把这个打出来的时候，这开始听起来像是一个可行的选择。

**但是我当时没有想到！**

这是我想到的，以及二进制是如何产生的。在这个过程中，我们将使用一些简洁的 Python 标准库模块。

## 二进制:只是一串电灯开关

我开始思考组合的定义。对于每个容器，可能的选项是“使用它”或“不使用它”它要么在解决方案之内，要么在解决方案之外。嗯，听起来像是一排拨动开关排成一行。然后，我们需要以某种方式迭代各种可能性。

对于每个集装箱，我们有两种选择。对于两个容器，我们有 2 x 2 个选项。对于三个容器，我们实际上有 8 个选项:

```
no no no
no no yes
no yes no
no yes yes
yes no no
yes no yes
yes yes no
yes yes yes 
```

Enter fullscreen mode Exit fullscreen mode

8 个选项，或者 2 x 2 x 2，更好的说法是`2^3`。对于四个容器，每个容器有两个选项，那么就是 2×2×2×2 或`2^4`或 16 个选项。

因此，对于我们的输入，我们有 20 个容器。那是`2^20`选项，或者说 1，048，576 种可能性。实际上，总迭代次数相当多！可能有更好的方法，但是过早的优化是万恶之源，所以让我们先尝试一下更简单、更强力的方法，看看它是否足够快来满足我们的需求。

> 过早优化是万恶之源。
> ——唐纳德·克纳特

现在，我们只需要一个很好的系统化的方法来完成开/关的可能性，就像我们在上面的 8 个选项中所做的那样。除了输入那么多是和不是会让人精疲力尽。我将使用一种速记法，其中 1 表示是，0 表示否。我并不是在偷偷摸摸地这么做——你能看出它的走向吗？让我们列出我上面提到的四容器问题的所有可能性。

```
0000
0001
0010
0011
0100
0101
0110
0111
1000
1001
1010
1011
1100
1101
1110
1111 
```

Enter fullscreen mode Exit fullscreen mode

这有点像我们在计数，但数字只有两种可能性(1 或 0)，而不是 10 (0-9)。糟糕，我们刚刚重新发明了二进制。你可以从一英里外看到这一点。或者也许你不能，你只是第一次有二进制点击！恭喜你！无论哪种方式都可以，因为现在我们可以解决我们的蛋酒问题。

你知道二进制的好处吗？你可以把普通数字转换成二进制！因此，我们需要一个非常复杂和神秘的算法来计算常规数字:

```
for i in range(2**20):
    print(i) 
```

Enter fullscreen mode Exit fullscreen mode

Tada！我们刚刚发明了 for 循环！我们今天收获颇丰。

## 编程解决方案

太好了。我们的解决方案还需要三个步骤。

1.  我们如何将常规数字`i`转换成二进制？
2.  我们如何将*转换成列表中有用的容器选择？*
3.  我们如何检查容器的选择是否是问题的解决方案？

这可能看起来很多，所以我们会像你吃大象一样处理它:一次一口。一次一个字节？呃？呃？

[![Binary Joke FTW](img/932948787b7de56d2adfd881592a32f1.png "An eel looking like he's waiting for you to laugh at his joke.")](https://res.cloudinary.com/practicaldev/image/fetch/s--K4adFJwy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static-similan.similandivingtours.cimg/Blog/information/moral-eels-in-Thailand/Giant-Moray-eel.jpg)

### 转换成二进制

通常情况下，如果你只是想在 Python 中对一个数进行二进制运算，你甚至不用“转换”成二进制。这是初学者常见的错误，在 StackOverflow 上有大约十亿个错误答案。然而，在这种情况下，我们实际上希望迭代一个由 1 和 0 组成的列表，而不是一个完整的二进制数。有几种方法可以解决这个问题。不幸的是，最短的方法可能是滥用 Python 中的`bin`函数，该函数返回二进制数的*字符串*(例如`"0b0010101"`)。类似这样的*差不多*管用:

```
bits = [1 if bit == "1" else 0 for bit in bin(i)[2:]] 
```

Enter fullscreen mode Exit fullscreen mode

在开始时，您需要用`[2:]`去掉`0b`，在 string-land 短暂停留后，您需要返回整数。总的来说，不是我最喜欢的，但可能比其他选择更容易和简单。但是，因为这是从左到右检查数字，不同的数字有不同的位数，我们需要用零填充二进制数，以确保我们每次都从相同的数字开始。幸运的是，字符串有一个方便的`zfill`方法，可以在字符串前面加上零，直到它们达到我们想要的长度。

```
binary_number = bin(i)[2:].zfill(len(containers))
bits = [1 if bit == "1" else 0 for bit in binary_number] 
```

Enter fullscreen mode Exit fullscreen mode

嗯……我不知道。这开始看起来非常粗糙和模糊。就我个人而言，我更倾向于做这样的事情，我们计算它，更类似于数学实际上是如何工作的。

```
def bits(number):
    while number > 0:
        yield number % 2
        number >>= 1 
```

Enter fullscreen mode Exit fullscreen mode

> 注意:这是 Python 中的一个概念，可能有点陌生，叫做生成器函数。当我们调用这个函数时，它会创建一个生成器，我们可以迭代这个生成器来接收我们的值。或者，如果我们很贪婪，我们可以使用`list`函数将它们全部放入一个列表中。生成器比列表更有效一些，因为它很懒，一次只计算一个项目，很多时候，当你看到项目是如何在函数中生成的时候，实际上更容易推理，而不是出于某种原因在列表中理解。

如果一个数能被 2 整除，那么它最右边的位就是零。否则就是一。所以，我们在这里做的是产生数字的最右边的位，将数字向右移动以去掉该位，并继续下去，直到超出数字。理解起来更技术性一点，但是我们更少依赖于奇怪的 Python 特性，并且我们的意图更清晰。

我们可以这样使用:

```
>>> for bit in bits(i):
...     print(bit)
1
0
0
1
1
1

>>> bits(i)
<generator object bits at 0x10eaf3480>

>>> list(bits(i))
[1, 0, 0, 1, 1, 1] 
```

Enter fullscreen mode Exit fullscreen mode

现在，你会注意到这里有一个重要的警告。我们介绍的这两种方法以相反的顺序提供位*。这种方法是**“小端序”**，意味着它以最低有效位优先的顺序提供位。使用`bin`的另一种方法是**“Big Endian”**，这意味着它以最高有效位优先的顺序提供位。通常，这是非常重要的。然而，在我们的例子中，我们只想确保我们通过了所有的可能性，所以只要我们选择了一个方法，并与我们开始的数字保持一致(因此在第一个例子中是填充)，我们就不需要关心。*

好吧，这是对几行代码的大量解释，但我认为这是值得的。

### 用我们的钻头挑选容器

这个更简单。我们如何手动完成这项工作？我将分三个阶段向您展示，逐步提高:

```
result = []
for index in range(len(bits)):
    if bits[index] == 1:
        result.append(containers[index]) 
```

Enter fullscreen mode Exit fullscreen mode

很好，对吧？我们遍历具有相同索引的位和容器，如果该位为 1，则我们在相应的索引处获取容器。如果不是，我们就不要！电灯开关！它还处理了这样一个奇怪的事实，即一些较小的数字的位数比容器的位数少，而不必填充一堆奇怪的零(例如，`bits(7)` returns，`[1, 1, 1]`)，我们也不必再添加 13 个 0，这样就有了与容器相同的位数。相反，我们可以迭代这些位，当用完时就停止。

但是，我们使用指数，这是令人讨厌的。我们不得不键入`range(len(`的事实应该立即敲响警钟。一定有更好的办法！确实有。任何时候你发现自己在使用索引，因为你需要以某种方式将两个列表连接在一起，那么使用`zip`函数可能会更好。

```
result = [container for bit, container in zip(bits, containers) if bit == 1] 
```

Enter fullscreen mode Exit fullscreen mode

啊，好多了。没有索引，只是一个很好的干净的理解。

下一步我们可以走得更干净。我会说这可能是可选的。我们的代码非常干净。但是我想展示给你们看，因为这是一个简洁的函数。

```
from itertools import compress

result = compress(containers, bits) 
```

Enter fullscreen mode Exit fullscreen mode

没错！在我们所有的智慧中，我们已经重新发明了 Python 维护者已经给我们的东西。`itertools`中的`compress`函数接受两个 iterables(类似于`zip`)，如果第二个 iterables 中相应的元素是“truthy”，它就过滤第一个 iterables 这就是为什么我们需要将来自`bin`函数的数字从字符串转换成整数。虽然`1`是真的而`0`是假的，但不幸的是`"1"`和`"0"`都是真的，因为它们是非零长度的字符串，所以它们不适用于这种方法。

### 检查解决方案

在最后一步，我们需要检查这个容器集合是否是问题的解决方案:它加起来正好是 150 升的空间吗？

```
if sum(result) == 150:
    good_combinations.append(result) 
```

Enter fullscreen mode Exit fullscreen mode

我不认为我们需要做更多。

## 把所有的东西放在一起

这是它看起来的样子。记住这个谜题是问我们有多少种不同的方法来储存 150 升。我精简了一些东西，使它们更加模块化，更容易连接。

```
"""Eggnog Storage: How can you store 150 liters exactly?"""

import itertools

def bits(number):
    while number > 0:
        yield number % 2
        number >>= 1

def perfect_fits(containers, target):
    for i in range(2**len(containers)):
        selection = itertools.compress(containers, bits(i))
        if sum(selection) == target:
            yield selection

if __name__ == "__main__":
    with open("containers.txt", "r") as f:
        containers = [int(line) for line in f.read().splitlines()]

    total_eggnog = 150  # liters
    good_options = perfect_fits(containers, total_eggnog)

    print("Combinations that work:", len(list(good_options)))

    # 4372 
```

Enter fullscreen mode Exit fullscreen mode

注意:如果这么多的生成让你有点紧张，这里有一个版本可以用你可能更熟悉的方式来创建列表。

```
"""Eggnog Storage: How can you store 150 liters exactly?"""

def bits(number, width):
    binary_number = bin(number)[2:].zfill(width)
    return [1 if bit == "1" else 0 for bit in binary_number]

def perfect_fits(containers, target):
    results = []
    for i in range(2**len(containers)):
        selection = [container
                     for container, bit 
                     in zip(containers, bits(i, len(containers))
                     if bit == 1]
        if sum(selection) == target:
            results.append(selection)

    return results

if __name__ == "__main__":
    with open("containers.txt", "r") as f:
        containers = [int(line) for line in f.read().splitlines()]

    total_eggnog = 150  # liters
    good_options = perfect_fits(containers, total_eggnog)

    print("Combinations that work:", len(good_options))

    # 4372 
```

Enter fullscreen mode Exit fullscreen mode

我个人认为不太漂亮，但完全可行。现在我们可以拿着所有的蛋酒了！

## 二进制 FTW！

希望我们没有在技术实现、生成器、字符串与数学比特等问题的杂草中迷失太多。我想在这里展示的是，本质上，我们使用二进制来允许我们做一个简单的计数循环，来帮助我们选择所有不同的容器组合，二进制并不可怕。我再说一遍。

> 二进制并不可怕——它只是带电灯开关的数学。

我的目标是鼓励你将二进制代码添加到你的工具箱中，至少在你发现自己正在处理一组可能被选择也可能不被选择的项目时，考虑一下它。这可能是比萨饼上的配料。也许是 RPG 中不同的状态效果。中毒了，烧伤了，困了？`111`，或`status = 7`。只是中毒了，困了？`101`，或`status = 5`。只烧了？`010`，或`status = 2`。

二进制很酷，你也很酷！