# 他们是如何在一条线上做到这些的？Codewars 挑战解释

> 原文：<https://dev.to/ekand/how-did-they-do-all-that-in-one-line-a-codewars-challenge-explanation-3e2c>

今天我在 Codewars 上做了一个挑战，在网站上看到另一个用户提交了一个非常简洁的解决方案。我将首先展示代码，然后解释发生了什么。

```
def up_array(arr):
  if not arr or min(arr) < 0 or max(arr) > 9:
    return None
  else:
    return [int(y) for y in str(int("".join([str(x) for x in arr])) + 1)] 
```

Enter fullscreen mode Exit fullscreen mode

挑战可以在这里找到，但是基本思想是获取一个整数列表，将该列表表示的值加 1，然后返回一个包含新值的新列表。

让我们来分解这个解决方案。

首先是有多个部分的 if 语句。`if`开始吧。然后就是表情`not arr`。这很好。它利用了像 list 这样的数据类型可以有布尔解释的事实。在这种情况下，如果数组为空，`not arr`将计算为`True`(因为`arr`将计算为`False`)。

通过`or`将其附加到下一位。接下来的两部分更简单。如果数组的最小值小于零，或者数组的最大值大于九，If 语句将计算为`True`。

因此，如果列表为空或者包含超出范围的元素，函数将返回`None`。

现在才是真正的关键。

`return [int(y) for y in str(int("".join([str(x) for x in arr])) + 1)]`
whew！那是一口。

在圆括号和中括号的中心，我们有:`str(x) for x in arr`。这似乎很简单。这似乎在功能上等同于下面的代码(伪代码):

```
y = []
for x in arr:
    y = str(x) 
```

Enter fullscreen mode Exit fullscreen mode

关键是，我认为`str(x) for x in arr`会返回一个由 arr 中的整数转换而来的字符串数组。

我们来测试一下这个。

```
In      arr = [2,3,9]

In      [str(x) for x in arr]
Out     ['2', '3', '9'] 
```

Enter fullscreen mode Exit fullscreen mode

是的。事情就是这样的。

下一步:`"".join([str(x) for x in arr])`。看起来我们正在将一个空字符串加入到字符串数组中。或者我们把字符串数组连接成一个数组，中间是空字符串。我可以测试这个。

```
In[4]:     [str(x) for x in arr]
Out[4]:    ['2', '3', '9']

In[5]:     "".join([str(x) for x in arr])
Out[5]:    '239'

In[6]      "-".join([str(x) for x in arr])
Out[6]:    '2-3-9' 
```

Enter fullscreen mode Exit fullscreen mode

是的。它将数组的字符串连接在一起，字符串之间没有任何内容("")或破折号("-")。

接下来呢？
我们有`(int("".join([str(x) for x in arr])) + 1)`。这很简单。取上面的结果，将其转换为整数，然后加 1。酷毙了。

接下来，我们有`str(int("".join([str(x) for x in arr])) + 1)`。这是一个简单的步骤。我们只是把得到的整数转换成一个字符串。

接下来的一点有点棘手。`[int(y) for y in str(int("".join([str(x) for x in arr])) + 1)]`。我相信这类似于:

```
new_list = []
for y in my_string:
    y = int(y)
    new_list.append(y) 
```

Enter fullscreen mode Exit fullscreen mode

所以它应该从字符串中取出每个字符，转换成一个整数，然后放到一个列表中。我们来测试一下。

```
In[7]:    [int(y) for y in str(int("".join([str(x) for x in arr])) + 1)]
Out[7]:   [2, 4, 0] 
```

Enter fullscreen mode Exit fullscreen mode

是的。这就是它的作用。很好。

那么就差一步了:`return [int(y) for y in str(int("".join([str(x) for x in arr])) + 1)]`。这意味着获取所有代码的结果，并将其作为函数输出返回。

## 总结

所以，总结一下这段代码，我们有:

```
def up_array(arr):
  if not arr or min(arr) < 0 or max(arr) > 9:
    return None
  else:
    return [int(y) for y in str(int("".join([str(x) for x in arr])) + 1)] 
```

Enter fullscreen mode Exit fullscreen mode

如果我把它翻译成伪代码，看起来会像:

```
define function up_array as a function of the array, arr:
    if there are problems with the input:
        return nothing
    otherwise:
        take a list of integers, convert each to a string and put them
        together into a string, convert that string to an int, add one to 
        that int, and convert that result to a string. Then pull out the 
        characters of that string one by one, converting each into a 
        string and populating a list with those strings.
        Return that list. 
```

Enter fullscreen mode Exit fullscreen mode

从这篇文章中我学到了很多，我希望你能从阅读中有所收获。