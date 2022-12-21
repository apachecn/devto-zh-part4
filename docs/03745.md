# Java 日常编码问题#007

> 原文：<https://dev.to/awwsmm/java-daily-coding-problem-007-366n>

[日常编码问题](https://dailycodingproblem.com/)是一个网站，它会每天向你的收件箱发送一个编程挑战。我想向初学者展示如何使用 Java 解决这些问题，所以这将是我的一系列解决方案中的一个。欢迎在评论中把它们挑出来！

### 问题

> 给定映射 a = 1，b = 2，...z = 26，以及一个编码的消息，计算它可以被解码的方式的数量。
> 
> 例如，消息“111”将给出 3，因为它可以被解码为“aaa”、“ka”和“ak”。
> 
> 您可以假设这些消息是可解码的。例如，不允许使用“001”。

### 策略

*剧透！*不要看下面，除非你想看我的解决方案！

* * *

这个问题需要我们建立一个包含所有可能解决方案的二叉树，然后计算这棵树的叶子数。例如，如果我们得到消息`11212`，我们的树可能看起来像:

```
start ------------  1 --------  1 ------  2 ----  1 -- 2
      \               \           \         \
       \               \           \         `-- 12
        \               \           \
         \               \           `-- 21 ----  2
          \               \
           \               `-- 12 ------  1 ----  2
            \                     \
             \                     `---- 12
              \
               `-- 11 --------  2 ------  1 ----  2
                      \           \
                       \           `---- 12
                        \
                         `---- 21 ------  2 
```

Enter fullscreen mode Exit fullscreen mode

我说*二叉树*是因为字母表中只有 26 个字母，所以我们只对 1 位和 2 位整数的子集感兴趣。这意味着，在消息中的任何给定点，下一个字符和/或下两个字符是一个字母的合法编码。所以树的每个节点最多只能有两个子节点。

在上面的例子中，您可以计算没有子节点(叶子)的八个节点，这表明编码的消息`11212`有八个合法解码。事实证明，对于长度为 5 的消息，这是合法编码的最大可能数量，因为在消息中的任何点，后面至少有两位数，接下来的两位数是字母的合法编码，接下来的一位数也是。

但是提示说信息必须是可解码的，那么为什么还要提到这一点呢？

嗯，有可能消息是可解码的，而消息中的任意两位数是不可解码的。例如，以消息
为例

```
dangitbobby 
```

Enter fullscreen mode Exit fullscreen mode

编码，这是

```
411479202152225 
```

Enter fullscreen mode Exit fullscreen mode

这样写出来，很明显有一些 2 位数的序列不是有效的字母。比如`47`或者`92`或者`02`。这些“不可解码的子序列”的存在意味着该消息将比任意 15 位消息的最大可能解码数少。因此，我们有一个可能解码数量的上限，作为以数字表示的消息长度的函数。

对于 3 位数的消息，它是 3，如提示所示。对于一个 5 位数的消息，它是 8，如上所示。一般来说，一个 1 位数的消息只有一个合法解码，而一个 2 位数的消息最多有 2 个合法解码。通过“修剪”我们的二叉树，在第一个数字之后，我们可以看到一个 4 位数字的消息最多有 5 个合法编码:

```
start ------------  1 ----  /  ----  1 ------  2 ----  1 -- 2
                      \    /           \         \
                       \  /             \         `-- 12
                         /   \           \
                        /     \           `-- 21 ----  2
                       /       \
                      /         `-- 12 ------  1 ----  2
                     /                 \
                    /                   `---- 12 
```

Enter fullscreen mode Exit fullscreen mode

这个顺序，`1`，`2`，`3`，`5`，`8`，...你可能很熟悉。让我们在我们的 5 位数消息的开头再加一个数字(比如说，`2`，所以得到的消息是`211212`)，然后看看 6 位数消息合法解码的最大可能数量:

```
start ----------  2 ------------  1 --------  1 ------  2 ----  1 -- 2
      \             \               \           \         \
       \             \               \           \         `-- 12
        \             \               \           \
         \             \               \           `-- 21 ----  2
          \             \               \
           \             \               `-- 12 ------  1 ----  2
           |              \                     \
           |               \                     `---- 12
           |                \
           |                 `-- 11 --------  2 ------  1 ----  2
           |                        \           \
           |                         \           `---- 12
           |                          \
           |                           `---- 21 ------  2
            \
             `-- 21 ------------  1 --------  2 ------  1 ----  2 
                    \               \           \
                     \               \           `---- 12
                      \               \
                       \               `---- 21 ------  2
                        \
                         `------ 12 --------  1 ------  2
                                    \
                                     `------ 12 
```

Enter fullscreen mode Exit fullscreen mode

当我们有一个 6 位数的信息时，最大可能解码数是 13。我们现在看到这是斐波纳契数列:`1`、`2`、`3`、`5`、`8`、`13`，...一个`N`数字序列的最大可能编码数是`F(N)`，其中`F(N)`是第`N`个斐波那契数、`F(1) = 1`和`F(2) = 2`。这给了我们一个对我们的解决方案的健全性检查，实际上我们应该回到这个问题上来。

那么我们该如何着手呢？最直接的方法是遍历树，一次一个数字，检查下两个数字是否是字符的有效编码(下一个数字*几乎总是*是有效编码，除非是`0`)。如果是，在我们的解决方案中创建一个分支并继续。最简单的编码方法是通过递归算法。也许我们可以用一种聪明的方式来写，这样编译器可以做一些[尾部调用优化](https://stackoverflow.com/questions/310974/what-is-tail-call-optimization)？

### 代码

我的直觉告诉我，我们不想做太多的`String`操作，所以让我们首先获取给定的消息，并将其转换为一个`int`数组。类似于:

```
jshell> Stream.of("1234567".split("")).mapToInt(s -> Integer.parseInt(s)).toArray()
$15 ==> int[7] { 1, 2, 3, 4, 5, 6, 7 } 
```

Enter fullscreen mode Exit fullscreen mode

让我们用一个函数来总结一下:

```
public class Message {

  public static int[] digitise (String message) {
    return Stream.of(message.split("")).mapToInt(s -> Integer.parseInt(s)).toArray();
  }

} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们需要一个函数来查看接下来的两位数字，并确定它们是否是有效的字符编码(如果它们形成一个小于`27`的数字):

```
 public static boolean startsWithValidTwoDigitSeq (int[] message) {
    if (message.length < 2) return false;
    if (message[0] > 2 || message[0] < 1) return false;
    if (message[0] == 2 && message[1] > 6) return false;
    return true;
  } 
```

Enter fullscreen mode Exit fullscreen mode

现在，给定一个特定的消息，我们可以检查接下来的两个数字是否编码一个字符。如果有，我们分叉搜索，用两个新的消息字符串创建两个新的搜索——一个删除了第一个数字，另一个删除了前两个数字。

*有可能第一个数字是一个`0`，在这种情况下，第一个数字和前两个数字都没有编码一个有效的字符。我们中止搜索。*

每当我们到达一条有效消息的末尾时(意味着我们到达了最后一个或两个数字，这些数字是一个字符的有效编码)，我们就增加总的有效解码数。

这个简单的过程表示如下:

```
 public static int sum = 0;

  public static int nValidDecodings (int[] digits) {
    sum = 0;
    sumDecodings(digits);
    return sum;
  }

  private static void sumDecodings (int[] digits) {

    // (1) if there are no digits left...
    if (digits.length < 1) return;

    // (2) if there's only 1 digit left...
    if (digits.length < 2) {

      // (2a) ...and it's not a valid encoding of a character
      if (digits[0] == 0) return;

      // (2b) ...and it's a valid encoding of a character
      sum += 1;
      return;
    }

    // (3) if there are only 2 digits left...
    if (digits.length < 3) {

      // (3a) ...and it's a valid encoding of a character
      if (digits[0] == 1 || (digits[0] == 2 && digits[1] < 7)) sum += 1;

      // (3b) ...fork by removing first digit
      sumDecodings(Arrays.copyOfRange(digits, 1, digits.length));
      return;
    }

    // (4) if there are 3+ digits left...

    // (4a) ...and the first digit is not a valid encoding of a character:
    if (digits[0] == 0) return;

    // (4b) ...and the first two digits are a valid encoding of a character
    if (digits[0] == 1 || (digits[0] == 2 && digits[1] < 7)) {

      // ...fork with 1-digit number and 2-digit number removed
      sumDecodings(Arrays.copyOfRange(digits, 1, digits.length));
      sumDecodings(Arrays.copyOfRange(digits, 2, digits.length));
      return;

    }

    // (4c) ...and the first digit is a valid encoding of a character:
    sumDecodings(Arrays.copyOfRange(digits, 1, digits.length));
    return;

  } 
```

Enter fullscreen mode Exit fullscreen mode

像大多数这些难题的解决方案一样，我们从边缘案例开始，并以我们的方式向后工作。最简单的情况发生在我们传递没有数字的消息时:

```
 // (1) if there are no digits left...
    if (digits.length < 1) return; 
```

Enter fullscreen mode Exit fullscreen mode

...我们不增加类变量`sum`并简单地中止流程(或流程的这个特殊分支)。下一种情况发生在消息只有一位数的时候:

```
 // (2) if there's only 1 digit left...
    if (digits.length < 2) {

      // (2a) ...and it's not a valid encoding of a character
      if (digits[0] == 0) return;

      // (2b) ...and it's a valid encoding of a character
      sum += 1;
      return;
    } 
```

Enter fullscreen mode Exit fullscreen mode

...如果单个数字是一个`0`，该消息无效，我们中止而不增加`sum`。否则，增加`sum`和*，然后*退出。请注意，这可以重新排列，从而显著减少行数:

```
 // (2) if there's only 1 valid digit left...
    if (digits.length < 2 && digits[0] != 0) sum += 1;
    return; 
```

Enter fullscreen mode Exit fullscreen mode

...但我觉得前者更清晰一点。第三种情况发生在消息正好有两位数的时候。这里我们需要关注分叉过程:

```
 // (3) if there are only 2 digits left...
    if (digits.length < 3) {

      // (3a) ...and it's a valid encoding of a character
      if (digits[0] == 1 || (digits[0] == 2 && digits[1] < 7)) sum += 1;

      // (3b) ...fork by removing first digit
      sumDecodings(Arrays.copyOfRange(digits, 1, digits.length));
      return;
    } 
```

Enter fullscreen mode Exit fullscreen mode

如果消息中只有两位数字，并且这些数字是字符的有效编码，我们将增加有效编码的总数`sum`。不管那个两位数的数字是否是有效的编码，我们通过移除数组的第一位数字(使用`Arrays` API)并将结果数组传递回`sumDecodings()`来考虑接下来的两个一位数都是有效编码的可能性。

最后一种也是最复杂的情况发生在我们的消息中有两个以上的数字:

```
 // (4) if there are 3+ digits left...

    // (4a) ...and the first digit is not a valid encoding of a character:
    if (digits[0] == 0) return;

    // (4b) ...and the first two digits are a valid encoding of a character
    if (digits[0] == 1 || (digits[0] == 2 && digits[1] < 7)) {

      // ...fork with 1-digit number and 2-digit number removed
      sumDecodings(Arrays.copyOfRange(digits, 1, digits.length));
      sumDecodings(Arrays.copyOfRange(digits, 2, digits.length));
      return;

    }

    // (4c) ...and the first digit is a valid encoding of a letter:
    sumDecodings(Arrays.copyOfRange(digits, 1, digits.length));
    return; 
```

Enter fullscreen mode Exit fullscreen mode

这里，有三个子案例。第一个`(4a)`，初始数字可能是零，在这种情况下，没有对该消息的有效解码，我们提前退出。第二个`(4b)`，消息的前两个数字可能是字符的有效编码，在这种情况下，我们分叉处理两次，一次是删除第一个数字，另一次是删除消息的前两个数字。最后`(4c)`，如果前两个数字不能组成一个字符的有效编码，那么至少第一个数字可以，在这种情况下，我们只需通过删除第一个数字来继续这个过程。

同样，为了简洁起见，这最后一步可以稍微重新安排一下:

```
 if (digits[0] != 0) {

      if (digits[0] == 1 || (digits[0] == 2 && digits[1] < 7)) {
        sumDecodings(Arrays.copyOfRange(digits, 2, digits.length));
      }

      sumDecodings(Arrays.copyOfRange(digits, 1, digits.length));
    }

    return; 
```

Enter fullscreen mode Exit fullscreen mode

...但是我认为我最初写的方式更清晰一些。最后，请注意，无论我们在哪里:

```
 if (digits[0] == 1 || (digits[0] == 2 && digits[1] < 7)) 
```

Enter fullscreen mode Exit fullscreen mode

...我们可以用我们之前写的助手函数来代替它:

```
 if (startsWithValidTwoDigitSeq(digits)) 
```

Enter fullscreen mode Exit fullscreen mode

完整的解决方案可以在我的 GitHub 上找到[。](https://github.com/awwsmm/daily/tree/master/007)

...但是这种解决方案有效吗？到目前为止，它似乎对我们看到的所有例子都有效:

```
jshell> /open Message.java

jshell> Message.nValidDecodings(Message.digitise("111"))
$80 ==> 3

jshell> Message.nValidDecodings(Message.digitise("11212"))
$81 ==> 8

jshell> Message.nValidDecodings(Message.digitise("1212"))
$82 ==> 5

jshell> Message.nValidDecodings(Message.digitise("211212"))
$83 ==> 13 
```

Enter fullscreen mode Exit fullscreen mode

...给出了我们期望的斐波那契数列。当我们输入导致无效字符编码的数字时也是如此:

```
jshell> Message.nValidDecodings(Message.digitise("131"))
$85 ==> 2

jshell> Message.nValidDecodings(Message.digitise("333"))
$86 ==> 1

jshell> Message.nValidDecodings(Message.digitise("11111"))
$87 ==> 8

jshell> Message.nValidDecodings(Message.digitise("11191"))
$88 ==> 5

jshell> Message.nValidDecodings(Message.digitise("11911"))
$89 ==> 6

jshell> Message.nValidDecodings(Message.digitise("19111"))
$90 ==> 6

jshell> Message.nValidDecodings(Message.digitise("91111"))
$91 ==> 5 
```

Enter fullscreen mode Exit fullscreen mode

### 讨论

原来如此！我们需要在开始时做一些机械的事情，将`String`转换成`int`数组等等，但是在最初的障碍之后，我们只需要仔细考虑边缘情况。

在试图解决这个问题的时候，我确信有一些不涉及遍历树的解析解决方案。但是我找不到。如果有人能帮忙，我将不胜感激！

* * *

我的日常编码问题解决方案的所有代码都可以在[github.com/awwsmm/daily](https://github.com/awwsmm/daily)获得。

建议？请在评论中告诉我。

如果你喜欢这篇文章，请考虑给我买杯咖啡来支持我的工作！