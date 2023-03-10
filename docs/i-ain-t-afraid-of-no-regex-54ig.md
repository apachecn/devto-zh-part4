# 我不怕没有正则表达式

> 原文：<https://dev.to/buojira/i-ain-t-afraid-of-no-regex-54ig>

有时候，知道你想做什么和不想做什么和知道你想做什么一样重要。这对于正则表达式 *(regex)* 来说是正确的，但是对于这篇文章来说也是正确的，因为它不适合那些已经习惯于日常使用 regex 的人。

但是，如果您想要一些可以理解的例子以便更经常地使用它，我希望这一系列文章可以帮助您。 *( [这里](https://en.wikipedia.org/wiki/Regular_expression)是一些历史，如果你想)*

下面所有的例子都可以在我的 [github 库](https://github.com/buojira/samples/tree/master/regexing)中找到，尽管如果你想在阅读的时候测试表达式，我推荐使用一些实时解析它的页面。我喜欢用 https://rubular.com/的 T2，但也可以随意选择你的。现在让我们编写代码:
在你的类中导入这个包:

> 导入 Java . util . regex . matcher；
> 导入 Java . util . regex . pattern；

假设您想要查找文本中所有以*“thing”*结尾单词。你可以这样做:

```
Pattern pattern = Pattern.compile("\\w+thing");
Matcher matcher = pattern.matcher("A thing I want is to find something, or anything. I do not really care, but I do no want go with nothing at hand.");
while (matcher.find()) {
    System.out.println("Found " + matcher.group());
} 
```

如果您想在不使用任何正则表达式的情况下获得完全相同的结果，它会显示如下:

```
String text = "A thing I want is to find something, or anything. I do not really care, but I do no want go with nothing at hand.";
String sufix = "thing";
String[] words = text.split(" ");
for (int i = 0; i < words.length; i++) {
    String word = words[i];
    if (word != null && word.contains(sufix)) { //"endsWith does not work because of "," and "."
        if (word.length() != sufix.length()) { //remember, you want words ending with "thing", but not the words itself
            System.out.println(word
                    .replace(".", "")
                    .replace(",", "")
            );
        }
    }
} 
```

注意，这是一个简单的例子。在更复杂的场景中，您需要手动检查许多其他东西。

但是让我们继续，好吗？

*"\\w+thing"* 是什么意思？好了，*【东西】*就是你想要的苏菲，相信这是相当明显的，让我们来看看 *"\\w+"* 。

### “\ \”

当你看到两个反斜杠时，它仅仅意味着一个转义字符对另一个转义字符进行转义。所以读成只有一个反斜杠(*"[\ w]+thing "*)；

### \w

表示任何单词字符。从 A 到 Z 的任意字母*(和 A 到 Z)* ，任意数字和*“_”。你能换一种方式写吗？是的，regex *"[a-zA-Z_]+thing"* 有完全一样的结果*(我们就只说括号)*。我你相信，因为这个变体如果更明确的话会更容易维护下去。像大多数事情一样，正则表达式有很多方法可以得到相同的结果。所以括号...*

### [ ]

意思是你要找的字符的选项。如果你只给 a*【a】*或*【b】*，你就写*【ab】*。如果你想要小写字母 a 到 z，你可以写*【a-z】*，如果你只想要小写字母 a 到 h，写*【a-h】*等等。哦...您会注意到，样本中的括号比需要的多得多，但结果是一样的。；

### +

这不是追加操作。 *"+"* 表示左边的字符是强制的。如果你把它换成一个*“*”*，意思是可选的，你会看到单词*“东西”* I 现在也显示出来了

继续吧。试试看。我会在这里等...

现在，如果你取这个操作符 *("\wthing")* 你会看到一个不同的结果。它将带来以下价值:

> 什么都没有

因为匹配器会理解你想要在*“thing”之前的任何单词字符。*但只有一个。你想要两个吗？使用*" { 2 } thing "*，您将获得:

> 什么都没有

你想在 sufix 之前至少有三个，但不想限制尺寸吗？使用 *"\w{3，}事"*、*"无事"*不会带来:

> 任何事情

你想要至少一个字符，但不超过三个？试试 *"\w{1，3}thing"* :

> 什么都没有

现在你可能会想，你不希望在你的结果破碎的话。试试 *"\W\w{1，3}thing"* 。此 *"\W"* 表示任何非文字字符。*【w】*的准确反义词。结果将是:

> 什么都没有

我也可能被写成*" { 1，3}thing"* 。 *"\s"* 表示任何空白字符(是的， *"\S"* 表示任何非空白字符)。

作为一名开发人员，你可能会想*“如果在短语的开头有一个目标词，会发生什么？”* ~~(在知识库中有一个解决方案。它不使用纯粹的正则表达式来解决，但是，嘿，我们不局限于一个纯粹的解决方案，对不对？)~~

尝试尽可能多的变化。

稍后在更复杂的环境中再见。