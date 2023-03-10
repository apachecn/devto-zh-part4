# 如何在 ruby 中返回多个值

> 原文：<https://dev.to/viricruz/how-to-return-multiple-values-in-ruby-1cb>

当您返回数据时，可以使用逗号分隔值在方法上返回`multiple values`。

查看下面的例子

```
def multiple_values
  return 1, 2, 3, 4
end 
```

Enter fullscreen mode Exit fullscreen mode

这里我们创建了一个名为`multiple_values`的方法，它返回 4 个值。它们中的每一个都用逗号隔开，当我们以这种形式调用`multiple_values`的结果时，我们会看到一个包含所有这些值的数组。因此，第一个值 1 将位于数组的`position 0`，2 将位于`position 1`，3 将位于`position 2`，最后一个值 4 将位于数组的`position 3`。

让我们称我们的`multiple_values`方法为

```
def print_values
  values = multiple_values

  _1 = values.first
  _2 = values[1]
  _3 = values[2]
  _4 = values[3]

  p "first - #{_1}, two - #{_2}, three - #{_3}, four - #{_4}" 
  # => "first - 1, two - 2, three - 3, four - 4"
end

print_values 
```

Enter fullscreen mode Exit fullscreen mode

### 调用方法

我们创建了一个名为`print_values`的方法，它将打印由`multiple_values`方法返回的数组的**值**。
如你所见，我们将`multiple_values`返回的数组存储在一个名为`values`的变量中，这个`values`现在包含了我们之前返回的所有值。因此，为了清楚地显示`values`包含的内容，我们为数组的每个值创建了一个*变量*。变量`_1`调用了数组的一个快捷方法来`retrieve`数组的第一个元素，尽管它的工作方式与`values[0]`完全相同。然后我们用剩余的变量`_2, _3, _4`来存储剩余的值。

在该方法的最后一行，我们使用自定义格式打印变量。所以为了使这个工作，不要忘记调用`print_values`来看看它做了什么。

如果你是那种喜欢玩代码的人。下面是现场版[https://repl.it/@ViriCruz/multiplevalues](https://repl.it/@ViriCruz/multiplevalues)的链接

请随意摆弄代码。