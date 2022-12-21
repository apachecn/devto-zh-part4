# 观察和观察

> 原文：<https://dev.to/petecorey/obverse-and-under-3mp>

我之前写过关于[使用](https://dev.to/blog/2019/08/26/prime-parallelograms/)[J 编程语言](https://www.jsoftware.com/)绘制一个“惊人的图形”。我找到的解决方案大概是这样的:

```
require 'plot'
f =: ] - [: #. [: |. #:
'type dot' plot f"0 p: i. 10000 
```

我们的动词`f`，通过明智地使用“capped”([`[:`]([【https://www.jsoftware.com/help/dictionary/d502.htm】)](https://www.jsoftware.com/help/dictionary/d502.htm)))动词串，采取了一种非常明确的方法。我们实质上是说，`f`是( [`=:`](https://www.jsoftware.com/help/dictionary/d001.htm) )给定数(【https://www.jsoftware.com/help/dictionary/d500.htm】()减去( [`-`](https://www.jsoftware.com/help/dictionary/d120.htm) )给定数的反基二( [`|.`](https://www.jsoftware.com/help/dictionary/d231.htm) )的基二( [`#.`](https://www.jsoftware.com/help/dictionary/d401.htm) )。

J 社区的几个成员向我指出，这个动词可以借助于“under”([`&.`](https://www.jsoftware.com/help/dictionary/d631.htm))连词来简化。让我们深入了解“下”是什么，以及如何使用它。

## 下什么？

考虑“下”( [`&.`](https://www.jsoftware.com/help/dictionary/d631.htm) )、[的最佳方式，正如 NuVoc 关于“下”](https://code.jsoftware.com/wiki/Vocabulary/ampdot)的页面所解释的那样，是根据[域](https://en.wikipedia.org/wiki/Domain_of_a_function)和进出这些域的转换来考虑。

> 动词 v 定义了论元(x 和)y 到 v 域的转换。接下来，动词 u 对转换后的参数进行操作。最后，将结果从 v 域转换回原始域。

在我们的例子中，输入的定义域是十进制，但是我们想要应用的转换(反转)需要发生在二进制定义域中。“下”( [`&.`](https://www.jsoftware.com/help/dictionary/d631.htm) )可用于将我们的输入转换为基数二( [`#:`](https://www.jsoftware.com/help/dictionary/d402.htm) )，应用我们的反转( [`|.`](https://www.jsoftware.com/help/dictionary/d231.htm) )，并将反转的结果转换回我们的原始基数十域，与我们的基数二动词的正面或反面，反基数( [`#.`](https://www.jsoftware.com/help/dictionary/d401.htm) ):

```
f =: ] - |. &. #: 
```

请注意，我们没有明确说明如何将反转的结果转换回原始域。j 知道 [`#:`](https://www.jsoftware.com/help/dictionary/d402.htm) 的正面是 [`#.`](https://www.jsoftware.com/help/dictionary/d401.htm) ，自动为我们套用。

开箱即用，J 带有许多正面配对。比如“开”( [`>`](https://www.jsoftware.com/help/dictionary/d020.htm) )，就是“盒”的正体( [`<`](https://www.jsoftware.com/help/dictionary/d010.htm) )，反之亦然。当对装箱值应用转换时，这种配对特别有用:

```
1+&.>1;2;3
┌─┬─┬─┐
│2│3│4│
└─┴─┴─┘ 
```

查看 J 文章末尾的正面对[的完整列表。](https://code.jsoftware.com/wiki/Fifty_Shades_of_J/Chapter_12#Obverse_to_Adverse)

## 推断的 Obverses

甚至由具有明确正面配对的动词组成的复合动词也可以与“under”([`&.`](https://www.jsoftware.com/help/dictionary/d631.htm))连用。j 将在没有任何干预或指导的情况下正确地推断和应用复合正面。

例如，如果我们想对一列值进行拆箱，然后在“平方根域”中处理它们(不管这意味着什么)，我们可以这样做:

```
1+&.([:%:>)1;2;3
┌────────────────┐
│4 5.82843 7.4641│
└────────────────┘ 
```

j 取每个值，打开它，找到它的平方根(`[:%:>`)，给结果加 1，然后平方并装箱(`[:*:<`)增加的值。

## 明确的目标

更有趣的是，如果给定的动词没有定义或推断出正序配对，J 让我们使用“正序”( [`:.`](https://www.jsoftware.com/help/dictionary/d311.htm) )动词来定义我们自己的配对。

作为一个例子，假设我们有一个 JSON 字符串保存一个值数组。我们希望解析我们的字符串，对这些值执行一些操作，然后将结果列表序列化回 JSON。

我们可以用[的`convert/json`包](https://github.com/jsoftware/convert_json)提供的`dec_json`和`enc_json`动词，告诉 J`dec_json`的正面是`enc_json` :

```
json =: dec_json :. enc_json 
```

在类似于`'[1, 2, 3]'`的 JSON 数组上运行`dec_json`将返回一个装箱的数字列表，所以我们希望打开每个箱子，执行我们的操作，并将结果装箱。这听起来像是“下”( [`&.`](https://www.jsoftware.com/help/dictionary/d631.htm) ):
的另一份工作

```
transform =: 1&+&.> 
```

所有这些加在一起，我们就可以执行我们的`transform`【下】( [`&.`](https://www.jsoftware.com/help/dictionary/d631.htm) )的`json`域:

```
transform &. json '[1, 2, 3]'
[2,3,4] 
```

而我们的结果就是 JSON 字符串`'[2,3,4]'`！

“Under”绝对是一个非常强大的连词，我可以看到自己以后会广泛使用它。感谢 J 社区的每一位好心指出并教我新东西的人！