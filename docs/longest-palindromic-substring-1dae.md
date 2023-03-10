# 最长回文子串

> 原文：<https://dev.to/rygelxvi/longest-palindromic-substring-1dae>

# 最长回文子串

### **如何找到字符串中最长的回文子串？**

### 首先我们要回答这个问题‘什么是回文？"

一个回文前后是一样的。

例如，这些都是回文

*   “妈妈”
*   “赛车”
*   《玉米卷猫》

那么当给定一个字符串时，我们如何确定它是否是一个回文呢？首先，我们需要确定我们是只考虑单个单词，还是考虑包含空格的情况。对于这种情况，我们将使用单个单词来简化。

一些更常见的解决方案包括“暴力”方法或检查每个子字符串，看看它是否是一个回文。我们也可以用从中心扩展的方法，这比暴力更有效。最好的方法是使用 Manacher 的算法，这是线性时间，我建议查找它。

在这篇博客中，我将解释如何用矩阵来解决这个问题。

需要跟踪 3 个变量。

1.  在回文开始处的索引(start)
2.  回文的长度(最大值)
3.  索引偏移(k)

```
def longest_palindrome(s)
   start = 0
   max = 0
   k = 2

end 
```

Enter fullscreen mode Exit fullscreen mode

我们将遍历字符串，当我们遇到匹配的字母(matrix[i][j])时，其中 matrix[i+1][j-1] == true，我们可以向表中添加另一个 true。要做到这一点，我们需要初始化矩阵，并设置所有矩阵[i][i] =真，因为每个字符是一个大小为 1 的回文。

```
 for n in 0..s.length - 1
      matrix[n][n] = true
    end 
```

Enter fullscreen mode Exit fullscreen mode

|  | a | b | b | b | a | b | c |
| --- | --- | --- | --- | --- | --- | --- | --- |
| a | T |  |  |  |  |  |  |
| b |  | T |  |  |  |  |  |
| b |  |  | T |  |  |  |  |
| b |  |  |  | T |  |  |  |
| a |  |  |  |  | T |  |  |
| b |  |  |  |  |  | T |  |
| c |  |  |  |  |  |  | T |

如果字符匹配，我们还需要遍历并设置 matrix[i][i+1]为真，否则奇数索引永远不会为“真”，当我们到达代码的这一部分时，您将会看到这一点。

```
 for m in 0..s.length - 1
      if (s[m] == s[m+1])
          matrix[m][m+1] = true
          start = m
          max = 1
      end
    end 
```

Enter fullscreen mode Exit fullscreen mode

|  | a | b | b | b | a | b | c |
| --- | --- | --- | --- | --- | --- | --- | --- |
| a | T |  |  |  |  |  |  |
| b |  | T | T |  |  |  |  |
| b |  |  | T | T |  |  |  |
| b |  |  |  | T |  |  |  |
| a |  |  |  |  | T |  |  |
| b |  |  |  |  |  | T |  |
| c |  |  |  |  |  |  | T |

现在，如果我们进行其余的比较，检查字符是否相同，矩阵值是否为左下方的真值([i+1][j-1])，我们将结束填充矩阵。

|  | a | b | b | b | a | b | c |
| --- | --- | --- | --- | --- | --- | --- | --- |
| a | T |  |  |  | T |  |  |
| b |  | T | T | T |  |  |  |
| b |  |  | T | T |  |  |  |
| b |  |  |  | T |  | T |  |
| a |  |  |  |  | T |  |  |
| b |  |  |  |  |  | T |  |
| c |  |  |  |  |  |  | T |

```
 while (k < s.length)
        while (i < s.length - k)
            j = i + k
            if (s[j] == s[i] && !!matrix[i+1][j-1])
                matrix[i][j] = true

                if (k > max)
                    max = k
                    start = i
                end

            end
            i += 1
        end
        k += 1
        i = 0
    end 
```

Enter fullscreen mode Exit fullscreen mode

我们从 k = 2 开始，因为我们已经填充了前两条对角线，然后每次增加 k 时，我们设置 i = 0，并在内部循环 j = i + k(偏移量)。最后，我们可以使用保存的变量返回子串。如果我们把它们放在一起，我们有...

```
def longest_palindrome(s)
    start = 0
    max = 0

    if (s.length < 3)
        return "" if (s.length < 1)
        return s[0] if (s.length == 1)
        return s[0] == s[1] ? s : s[0]
    end

    matrix = Array.new(s.length) {Array.new(s.length)}

    for n in 0..s.length - 1
      matrix[n][n] = true
    end

    for m in 0..s.length - 1
      if (s[m] == s[m+1])
          matrix[m][m+1] = true
          start = m
          max = 1
      end
    end

    i = 0
    k = 2
    while (k < s.length)
        while (i < s.length - k)
            j = i + k
            if (s[j] == s[i] && !!matrix[i+1][j-1])
                matrix[i][j] = true

                if (k > max)
                    max = k
                    start = i
                end

            end
            i += 1
        end
        k += 1
        i = 0
    end

    return s[(start)..(start + max)]
end 
```

Enter fullscreen mode Exit fullscreen mode

与往常一样，不要忘记添加您的基本案例！

```
 if (s.length < 3)

        # empty string as input
        return "" if (s.length < 1)

        # string of length 1 should return the char
        return s[0] if (s.length == 1)

        # If there are two letters that are the same return the string else just the first char
        return s[0] == s[1] ? s : s[0]
    end 
```

Enter fullscreen mode Exit fullscreen mode

请不要犹豫给我反馈我的代码！这是我们变得更好的唯一方法，我相信我还有很长的路要走=)