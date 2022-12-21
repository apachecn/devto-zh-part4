# 检查考试

> 原文：<https://dev.to/gsarwate/check-the-examination-3cl3>

## 简介

这是来自 [Codewars](https://www.codewars.com/kata/check-the-exam) 的问题。

## 问题

第一个输入数组包含考试的正确答案，比如["a "，" a "，" b "，" d"]。第二个是“答案”数组，包含学生的答案。

这两个数组不为空，并且长度相同。返回答案数组的分数，每个正确答案+4，每个错误答案-1，每个空白答案+0(空字符串)。

如果分数< 0，则返回 0。

例如:

```
checkExam(["a", "a", "b", "b"], ["a", "c", "b", "d"]) → 6
checkExam(["a", "a", "c", "b"], ["a", "a", "b",  ""]) → 7
checkExam(["a", "a", "b", "c"], ["a", "a", "b", "c"]) → 16
checkExam(["b", "c", "b", "a"], ["",  "a", "a", "c"]) → 0 
```

## 手动解决(人类智能)

问题提到两个数组。因为我们使用的是 Elixir，所以我们将它们称为列表。

步骤:

1.  从 0 分开始
2.  查看*正确答案列表*中的第一个条目
3.  看*答案列表*中与*正确 _ 答案列表*相同位置的条目
    *   如果答案条目为空，则加 0 分
    *   否则，将*正确答案列表*中的条目与*答案列表*中的条目进行比较
        *   如果两个条目都匹配，则在分数上加 4
        *   否则，从分数中减去 1
4.  查看*正确答案列表*中的下一个条目，如果条目存在，重复步骤 3
5.  如果*正确答案列表*中没有更多的条目
    *   如果分数为负，则分数为 0
    *   否则，报告分数并停止

## 溶液(仙丹)

我们可以并行遍历*正确答案列表*和*答案列表*，并按照手动解决方案中的概述对它们进行比较。但是，有更好的方法。

我们可以合并这两个列表，这样我们就可以遍历合并后的列表并比较列表中的条目来评分。

我们代码的结构将是:

```
Combine list
|> Check answers and determine the score of the exam
|> Report final score 
```

### 合并列表

仙丹函数[Enum.zip/2](https://hexdocs.pm/elixir/Enum.html#zip/2)允许我们将两个可枚举数(在我们的例子中是列表)中的相应元素压缩到一个元组列表中。

```
Enum.zip(correct_answer_list, answer_list) 
```

在我们的例子中，我们用 Enum.zip/2 函数组合两个可枚举数，但是仙丹函数[Enum.zip/1](https://hexdocs.pm/elixir/Enum.html#zip/1)也允许我们组合两个以上的可枚举数。

### 检查答案并确定考试分数

接下来，我们要检查列表中的条目。每个条目都是一个{correct_answer，answer}形式的元组。我们可以比较一个元组中的元素。在酏剂中有几种方法可以做到这一点。让我们来看看其中的一些方法。

#### 构建我们自己的函数来遍历列表

```
defp compare([], acc), do: acc

defp compare([head | tail], acc) do
  compare(tail, score(head, acc))
end 
```

在上面的代码中，我们创建了两个 compare/2 函数来遍历组合列表。它们都是私有函数，我们将从主函数中调用它们。

这些函数接受两个参数。第一个是我们之前构建的组合列表。第二个参数是累加器， *acc* ，我们用它来累加比较的结果。

这些函数允许我们递归地遍历列表**和**，同时累积每个比较的结果。

第一个函数是递归的“停止”条件。当我们到达列表的末尾时，我们停止并返回分数。注意，由于第二个函数调用自己作为最后一件事，它是一个*尾调用*，因此 Elixir/Erlang 将相应地执行尾调用优化。

```
defp score({_expected, answer}, acc) when answer == "", do: acc
defp score({expected, answer}, acc) when expected == answer, do: acc + 4
defp score({expected, answer}, acc) when expected != answer, do: acc - 1 
```

接下来，我们创建了三个从 compare/2 调用的私有 score/2 函数。它们允许我们比较预期的答案和实际的答案。它们允许我们使用多克劳斯函数进行条件逻辑的分支。

这些函数接受两个参数。第一个是一个形式为 *{expected_answer，answer}* 的元组，它是我们之前构建的组合列表的一个元素。第二个参数是一个累加器， *acc* ，我们用它来累加比较的结果。

我们可以使用条件结构，如 if、case 和 cond 等。在 compare/2 函数中。然而，通过使用多克劳斯函数，处理条件的逻辑与高级(调用)函数是分离的。条件的代码更加清晰和自描述。

这三个函数会更新 *acc* (分数)并返回给 compare/2 函数。

#### 使用 Enum 模块降低列表得分

```
Enum.zip(correct, answer)
|> Enum.reduce(0, &score/2) 
```

通过这种方法，我们使用酏剂[Enum.reduce/3](https://hexdocs.pm/elixir/Enum.html#reduce/3)模块遍历组合列表。该函数接受三个参数:

1.  可列举的(Enum.zip/2 编制的综合清单)
2.  累加器(存储归约结果)
3.  功能(比较答案)

累加器将从 0 开始(按照我们的手动步骤 1)。我们将使用 score/2 函数(如前所述)来计算分数。score/2 函数将获得两个参数——用于比较的元组和累加器。

### 报告最终得分

```
defp final_score(score) when score < 0, do: 0
defp final_score(score), do: score 
```

最后，我们可以比较分数来检查最终的分数。如果分数是负的，我们返回计数为 0。否则，我们将分数作为计数返回。

## 最终代码

```
defmodule CheckExam do
  def check_exam(correct, answer) do
    Enum.zip(correct, answer)
    |> Enum.reduce(0, &score/2)
    |> final_score()
  end

  defp score({_expected, answer}, acc) when answer == "", do: acc
  defp score({expected, answer}, acc) when expected == answer, do: acc + 4
  defp score({expected, answer}, acc) when expected != answer, do: acc - 1

  defp final_score(score) when score < 0, do: 0
  defp final_score(score), do: score
end 
```