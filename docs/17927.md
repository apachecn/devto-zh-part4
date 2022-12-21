# 欺骗谷歌的教程:用撇号编码信息

> 原文：<https://dev.to/healeycodes/a-tutorial-on-tricking-google-encoding-messages-in-apostrophes-45n4>

天才怀疑他们的歌词被谷歌删除，但他们需要一种方法来证明这一点。所以他们在一些歌词中加入了莫尔斯电码信息。上面写着**红色递给**。它是用不同类型的撇号编码的。我怀疑他们使用了 *U+0027 撇号*和 *U+2019 右单引号*，因为那些是最常用的，但我不能确定。

让我们写一个解决这个问题的方法:通过莫尔斯电码用撇号对信息进行编码。我们假设只有这两个撇号可用。我选择 Python 是因为它很适合字符串操作。

重要的部分:我们的两个标记，和莫尔斯字母表。

```
APOSTROPHE = '\''
RIGHT_MARK = '’'

morse = [".-", "-...", "-.-.", "-..", ".", "..-.", "--.", "....",
         "..", ".---", "-.-", ".-..", "--", "-.", "---", ".--.",
         "--.-", ".-.", "...", "-", "..-", "...-", ".--", "-..-",
         "-.--", "--.."] 
```

首先，我们需要一种将文本转换成莫尔斯电码的方法。`'cat'`变成了`'-.-..--'`(在某种程度上看起来像一只熟睡的猫！).

```
# flatten a 2D list flatten = lambda l: [item for sublist in l for item in sublist]

# :text: a message made up of [a-zA-Z] def to_morse(message):
    m = [morse[ord(c)-97] for c in message.lower()]
    return flatten(m) 
```

我选择了*撇号*来代表莫尔斯中的点，因为它具有较低的 Unicode 值。*右边单引号*代表破折号。一旦我们有了莫尔斯电码的信息，我们就用替换法把它转换成撇号信息。然后，我们迭代源文本，直到我们遇到一个撇号，并将其替换为消息中的下一个字符。看一看。

```
# :message: a message to encode
# :source: a source text with apostrophes def insert_morse(message, source):
    # a list of dots and dashes
    morse = to_morse(message)
    # a list of the two types of apostrophes
    secret = [APOSTROPHE if m == '.' else RIGHT_MARK for m in morse]
    inserted = []
    for c in source:
        if len(secret) > 0 and (c == APOSTROPHE or c == RIGHT_MARK):
            inserted.append(secret.pop(0))
        else:
            inserted.append(c)
    return ''.join(inserted) 
```

如果你想知道如何解码包含这种信息的原始文本，实际上要简单得多。

```
# :source: a source encoded via insert_morse def retrieve_morse(source):
    morse = []
    for c in source:
        if c == APOSTROPHE:
            morse.append('.')
        elif c == RIGHT_MARK:
            morse.append('-')
    return ''.join(morse) 
```

让我们来看一个实际例子。

```
source_text = 'Hello \'\' World, \'\' no \'\' secrets \' here!'
secret = 'cat'

encoded_message = insert_morse(secret, source_text)
retrieved_message = retrieve_morse(encoded_message)

print(encoded_message) # Hello ’' World, ’' no '’ secrets ’ here! print(retrieved_message) # -.-..-- 
```

相当整洁。如果你认为这很酷，那就等着看人们是如何使用零宽度空格来达到更高级的指纹欺骗吧。

解码**和**理解没有空格的莫尔斯电报的练习留给了读者！我很想听听你关于在网上隐藏数据的想法和资源。

从 GitHub 上的这篇文章[中抓取代码。](https://github.com/healeycodes/hidden-in-apostrophes)

* * *

加入我的关于编程和个人成长的[时事通讯](https://buttondown.email/healeycodes)的 150 多人注册！

我发关于科技的微博。