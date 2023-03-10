# 数据压缩入门

> 原文：<https://dev.to/awwsmm/a-primer-on-data-compression-46c2>

## 可能的状态

让我们讨论一下统计力学。假设我们有一个由 4 个粒子组成的系统，标记为`a`到`d`，每个粒子可以处于两种状态之一。我们可以称它们为`0`状态和`1`状态。一个粒子在`1`态时有一个“单位”的能量，在`0`态时没有能量。

```
1-energy:   x x 
0-energy: x     x
particle: a b c d 
```

Enter fullscreen mode Exit fullscreen mode

如果我们只测量系统的总能量，我们可以找到从`0`单位能量(当所有粒子都处于`0`态)到`4`单位能量(当所有粒子都处于`1`态)的任何值。

```
1-energy:              1-energy: x x x x
0-energy: x x x x  =>  0-energy:        
particle: a b c d      particle: a b c d
--> total energy: 0    --> total energy: 4 
```

Enter fullscreen mode Exit fullscreen mode

假设我们测量系统的能量，我们发现`2`个能量单位。这告诉我们，正好有两个粒子处于`1`状态，但是它没有告诉我们*哪两个粒子处于该状态。系统的总能量被称为*宏观态*，而每个单个粒子的能量(`a=0`、`b=1`、`c=0`...)被称为*微态*。例如，单个微观状态可以映射到同一个宏观状态...* 

```
abcd = 0101

1-energy:   x   x
0-energy: x   x  
particle: a b c d
--> total energy: 2 
```

Enter fullscreen mode Exit fullscreen mode

和

```
abcd = 1100

1-energy: x x
0-energy:     x x
particle: a b c d
--> total energy: 2 
```

Enter fullscreen mode Exit fullscreen mode

...两者都有能量`2`(相同的宏观状态)，但是哪一个粒子处于`0`或`1`状态在两个微观状态之间是不同的。在这样一个只有两种能量状态的系统中，粒子`a`可以处于`0`态或`1`态(`2`可能态)，而(独立地)粒子`b`可以处于`0`态或`1`态(`2`可能态)，等等。这些`2`叠加在一起，给我们`2^N`可能的微观状态，一个`2`-状态，`N`-粒子系统，其中粒子是可区分的(就像在我们的例子中一样——它们被标记)。

如果每个粒子有 50/50 的机会处于`0`态或`1`态，那么一个`2`能量系统就是最有可能的宏观态。以 50/50 的`0` / `1`概率，每个*微观状态*是同样可能的，并且最可能的`2`能量宏观状态简单地具有映射到它的最多微观状态:

```
abcd -> total energy
0000 -> 0
0001 -> 1
0010 -> 1          x
0011 -> 2          x
0100 -> 1        x x x
0101 -> 2        x x x
0110 -> 2        x x x
0111 -> 3      x x x x x
1000 -> 1  =>  0 1 2 3 4
1001 -> 2
1010 -> 2       energy
1011 -> 3      histogram
1100 -> 2
1101 -> 3    2-energy state
1110 -> 3   occurs 6/16 times
1111 -> 4 
```

Enter fullscreen mode Exit fullscreen mode

虽然`3`-能量宏观状态意味着一个给定的粒子有 75%的机会处于`1`位置，但是`2`-能量状态意味着平均而言，系统中的任何粒子都有相等的概率处于`0`状态或`1`状态。

## 那又怎样？

统计力学为热力学奠定了基础，热力学涉及热、压力和熵等概念，后者与信息理论直接相关。看起来很奇怪，物理定律可能与信息有关，这似乎是人类特有的概念，但熵将这两个领域联系在一起。

在我们上面的四粒子例子中，我们可以通过简单地应用公式
来计算系统的*熵*

```
S = k * ln(H) 
```

Enter fullscreen mode Exit fullscreen mode

...其中`H`是微观状态的数量，`k`是[玻尔兹曼常数](https://en.wikipedia.org/wiki/Boltzmann_constant)。`ln()`是自然(底数- `e`)对数。我们假设环境有足够的能量给予我们的粒子，因此任何微观状态都是可能的，而且可能性相当大。四粒子双态系统的熵是:

```
S = k * ln(16)
  =~ 2.77*k 
```

Enter fullscreen mode Exit fullscreen mode

在我们将其与其他系统的熵进行比较之前，这并没有多大意义。从某种意义上说，熵与信息量有关。在物理学中，熵还与系统的无序程度有关——越无序意味着越熵。让我们看另一个例子。假设我们冷却系统，带走所有的热量。在这种情况下，可能没有足够的能量使任何粒子进入`1`状态，所有粒子都停留在`0`状态。系统能量为零时允许的微观状态只有一个，熵变成:

```
S = k * ln(1)
  = 0 
```

Enter fullscreen mode Exit fullscreen mode

熵为零。系统是完美有序的；所有粒子都处于`0`状态，这种情况不可能改变。

## 比特熵，比特信息

让我们转向更大的粒子集合。假设我们有整个字母表，除了(稍后你会明白为什么)`y`和`z`——24 个标记为`a`到`x`的粒子——它们中的每一个都可以有`0`能量或`1`能量，同样有 50/50 的概率处于任一状态。凭直觉，哪种状态似乎不太可能偶然发生...

```
case (A):
100101011010100101011010
abcdefghijklmnopqrstuvwx 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
case (B):
111111111111000000000000
abcdefghijklmnopqrstuvwx 
```

Enter fullscreen mode Exit fullscreen mode

...？如果你认为`case (B)`看起来不太可能，你并不孤单。大多数人凭直觉认为第二种情况比第一种情况“可能性更小”——即使我们说过每个粒子有 50%的机会处于任何一种状态！从纯粹的概率角度来看，这些状态中的每一个都有*同等的可能性*。(这也是为什么如果你认为 1-2-3-4-5 这些数字不太可能中奖，你就不应该去玩彩票。它们和其他 5 位数的组合一样有可能。)

这种错误的直觉之所以发生，是因为我们已经对粒子进行了排序。如果我把它们给你...

```
case (C):
101010101010100110101011
itjukvlwmxanbopcdqerfsgh 
```

Enter fullscreen mode Exit fullscreen mode

...`case (C)`看起来比`case (B)`更有可能还是更不可能？(它们实际上完全等价。)

但是如果我们坚持用某种有序的*可区分的*粒子，那么我们就可以通过这种有序来“处理”粒子。然后**`case (A)`和`case (B)`** 有严重的区别。这与当我们看到一个与过去不同的价值观时，我们有多“惊讶”有关。

让我们从左到右(即按字母顺序)处理`case (A)`。我们可以从假设`0`或`1`状态有 50/50 的概率开始。然后，随着我们获得新的信息，我们会调整我们的期望。双态系统的[香农熵](https://en.wikipedia.org/wiki/Entropy_(information_theory))的公式为:

```
H = -(p * log2(p)) - (q * log2(q)) 
```

Enter fullscreen mode Exit fullscreen mode

...其中`p`是`0`状态的概率，`q`是`1`状态的概率(反之亦然)。如果我们从假设每个状态的可能性相等开始，那么第一点信息(粒子`a`给我们:

```
H = -(0.5 * log2(0.5)) - (0.5 * log2(0.5))
  = -(0.5 * -1) - (0.5 * -1)
  = -(-0.5) - (-0.5)
  = 1 
```

Enter fullscreen mode Exit fullscreen mode

正好 1 比特的信息！这并不奇怪，是吗？不过，接下来的部分有点棘手。我们现在有一个数据点(粒子`a`)，我们正试图从中进行推断。简单的[频率主义概率](https://en.wikipedia.org/wiki/Frequentist_probability)现在告诉我们，由于到目前为止我们看到的 100%的(一)比特都是`1` ( `a=1`)，我们期望所有未来的粒子都处于`1`状态:

```
H = -(p * log2(p)) - (q * log2(q))
  = -(0 * (-infinity)) - 0
  = problem here 
```

Enter fullscreen mode Exit fullscreen mode

我们真正应该做的是使用概率的贝叶斯估计。生活中很少有事情是 100%肯定会发生的，所以上面的公式不适用于全有或全无的概率。不是 0% / 100%的概率，让那些概率任意接近 0 和 1，但实际上并没有达到。然后会发生什么？

```
limit of H as e -> 0
H = -(e * log2(e)) - ((1-e) * log2(1-e))
  = 0 - (1 * log2(1))
  = 0 - 0 = 0 
```

Enter fullscreen mode Exit fullscreen mode

如果我们过去看到的所有粒子/比特都有相同的值，那么我们期望下一个也会有相同的值。我们不需要看着它就能“知道”它的价值。下一个粒子给了我们`0`比特的新信息。

然而，当我们看到一个新的值时，情况就变了。在我们看过`case (A)`的`a`和`b`之后，我们现在(通过频率主义者的方法)认为任一状态都有 50/50 的概率，而`c`给了我们`1`全部的新信息。然而，粒子`d`才是真正有趣的地方。此时，我们已经看到了`a=1`、`b=0`和`c=0`。所以我们假设在`d`中有 1/3 的概率看到`1`，有 2/3 的概率看到`0` :

```
H = -(1/3 * log2(1/3)) - (2/3 * log2(2/3))
  = -(1/3 * -1.585) - (2/3 * -0.585)
  = -(-0.528) - (-0.390)
  = 0.918 
```

Enter fullscreen mode Exit fullscreen mode

粒子`d`给我们的信息*比`1`比特略少*！这是因为“惊喜”因素不是全有或全无，就像我们假设 100%的概率在`a`后看到`1`，但它也不会是*总*惊喜，就像`c`一样，当时我们有 50%的机会获得`0`或`1`。我们之前已经看到过`0`和`1`了，但是我们看到的`0`稍微多一点。所以我们预计会有`0`，但是也有可能是`1`。

我们可以向下找到`case (A)`的每一位，并计算每个粒子/每个位置包含的“熵”信息量(以位为单位:

```
case (A):
a:1 -> 1.000 bits (1/2 assumed probability of "1" energy state)
b:0 -> 0.000 bits (1/1 prior probability)
c:0 -> 1.000 bits (1/2 prior)
d:1 -> 0.918 bits (1/3)
e:0 -> 1.000 bits (2/4)
f:1 -> 0.970 bits (2/5)
g:0 -> 1.000 bits (3/6)
h:1 -> 0.985 bits (3/7)
i:1 -> 1.000 bits (4/8)
j:0 -> 0.991 bits (5/9)
k:1 -> 1.000 bits (5/10)
l:0 -> 0.994 bits (6/11)
m:1 -> 1.000 bits (6/12)
n:0 -> 0.996 bits (7/13)
o:0 -> 1.000 bits (7/14)
p:1 -> 0.997 bits (7/15)
q:0 -> 1.000 bits (8/16)
r:1 -> 0.997 bits (8/17)
s:0 -> 1.000 bits (9/18)
t:1 -> 0.998 bits (9/19)
u:1 -> 1.000 bits (10/20)
v:0 -> 0.998 bits (11/21)
w:1 -> 1.000 bits (11/22)
x:0 -> 0.999 bits (12/23) 
```

Enter fullscreen mode Exit fullscreen mode

每个粒子平均提供大约`0.96`比特的信息。接近——但不完全是——`1`。如果我们想**压缩**这些信息，在一点一点的基础上我们做不了什么。然而，您可能会注意到数据
中的**模式**

```
case (A):
10 01 01 01 10 10 10 01 01 01 10 10
ab cd ef gh ij kl mn op qr st uv wx 
```

Enter fullscreen mode Exit fullscreen mode

这个比特流是*明显*可压缩的，因为如果我们将它顺序分解成 2 比特块，我们永远看不到模式`00`或`11`。这意味着我们可以将这个流编码为:

```
case (A):
 B  A  A  A  B  B  B  A  A  A  B  B
10 01 01 01 10 10 10 01 01 01 10 10
ab cd ef gh ij kl mn op qr st uv wx

where A = 01, B = 10 
```

Enter fullscreen mode Exit fullscreen mode

当然，为了*解码*数据，我们必须知道这种编码，但是这可以节省存储信息的空间。有趣的是，这种表示实际上比未压缩版本(平均`0.89`)包含更少*的*熵信息

```
case (A):
ab:B -> 1.000 bits (1/2 assumed)
cd:A -> 0.000 bits (1/1 prior)
ef:A -> 1.000 bits (1/2)
gh:A -> 0.918296 bits (1/3)
ij:B -> 0.811278 bits (1/4)
kl:B -> 0.970951 bits (2/5)
mn:B -> 1.000 bits (3/6)
op:A -> 0.9985228 bits (4/7)
qr:A -> 1.000 bits (4/8)
st:A -> 0.991076 bits (4/9)
uv:B -> 0.970951 bits (4/10)
wx:B -> 0.99403 bits (5/11) 
```

Enter fullscreen mode Exit fullscreen mode

原因是编码中的那些长串的`0`和`1`。这个系列实际上可以进一步压缩，因为我们接下来可以允许替换`C=BAA`和`D=ABB`得到:

```
case (A):
       C        D        C        D
 B  A  A  A  B  B  B  A  A  A  B  B
10 01 01 01 10 10 10 01 01 01 10 10
ab cd ef gh ij kl mn op qr st uv wx 
```

Enter fullscreen mode Exit fullscreen mode

简而言之，这就是信息压缩对数字文件的作用。你[试图在数据中找到共同的模式](https://msu.edu/course/tc/848/webcasting/login/resources/compression/index.html)，并用更短的表示替换那些模式，稍后可以对其进行解码以恢复原始文件。

这对于上面的字节流很有效，因为在每一步，只有两个(或更少)可能的“超级模式”。这些可以编码成一个字节。在每一步，数据将被压缩 50%。但是，如果我们在原始位模式中只有一个`00`或`11`字符串，压缩就不会工作:

```
case (D):
      ??        D        C        D
 B  A  ?  A  B  B  B  A  A  A  B  B
10 01 11 01 10 10 10 01 01 01 10 10
ab cd ef gh ij kl mn op qr st uv wx 
```

Enter fullscreen mode Exit fullscreen mode

这个单一的`11`模式意味着我们在第一级压缩时需要一个新的模式，这将需要一个额外的位，这违背了压缩的初衷。这意味着我们根本不能压缩 2 位数据包中的数据*。(尽管有可能其他编码机制也能做到这一点。)*

 *我前面说过，`case (A)`和`case (B)`有明显的区别，如果我们为`case (B)` :
计算每个粒子所包含的熵的量，就可以清楚地看到这一点

```
case (A):
a:1 -> 1.000 bits (1/2 assumed probability of "1" energy state)
b:1 -> 0.000 bits (1/1 prior probability)
c:1 -> 0.000 bits (2/2 prior)
d:1 -> 0.000 bits (3/3)
e:1 -> 0.000 bits (4/4)
f:1 -> 0.000 bits (5/5)
g:1 -> 0.000 bits (6/6)
h:1 -> 0.000 bits (7/7)
i:1 -> 0.000 bits (8/8)
j:1 -> 0.000 bits (9/9)
k:1 -> 0.000 bits (10/10)
l:1 -> 0.000 bits (11/11)
m:0 -> 0.000 bits (12/12)
n:0 -> 0.391244 bits (12/13)
o:0 -> 0.591673 bits (12/14)
p:0 -> 0.721928 bits (12/15)
q:0 -> 0.811278 bits (12/16)
r:0 -> 0.873981 bits (12/17)
s:0 -> 0.918296 bits (12/18)
t:0 -> 0.949452 bits (12/19)
u:0 -> 0.970951 bits (12/20)
v:0 -> 0.985228 bits (12/21)
w:0 -> 0.994030 bits (12/22)
x:0 -> 0.998636 bits (12/23) 
```

Enter fullscreen mode Exit fullscreen mode

...平均每个粒子只有`0.43`比特的信息。长串的`0`和`1`也是[压缩的时机](https://en.wikipedia.org/wiki/Run-length_encoding)。

## 总结

那么我们学到了什么？在物理层面上，信息内容与系统中固有的无序有关。一个系统越混乱，它包含的信息就越多。由一百万个零组成的文件是完全有序的，但是它包含的信息很少。

数据压缩依赖于这样一个事实，即在文件中，有时会有重复的比特或字符串，可以对这些字符串进行编码，以最小化编码文件的大小。当没有可辨别的模式或比特/字符的规则、重复运行时，包含在数字文件的比特中的“信息密度”(在物理/熵的意义上)被最大化。尽管两个文件可能包含相同的`1`和`0`的*号*，需要相同数量的*存储空间*在磁盘上，这些`1`和`0`在文件中出现的顺序是至关重要的。最大信息密度表现为一个充满完全随机的`1`和`0`的文件*