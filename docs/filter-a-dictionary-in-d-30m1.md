# 在 D 中过滤词典

> 原文：<https://dev.to/jessekphillips/filter-a-dictionary-in-d-30m1>

这不仅仅是过滤内容，而是有一个字典列表，并过滤特定数据。

```
auto data = [["key": 5], ["key": 2]];

auto foo = data.filter!(x => x["key"] == 5); 
```

Enter fullscreen mode Exit fullscreen mode

我在 count 示例中展示了这一点，这是我喜欢 d 的原因之一。我一直使用相同的工具，它们的工作方式有相似之处。挑战之一可能是产生遍历的能力，让我深入研究一下这个字典过滤。

```
import std.array;

auto dict = ["one" : 1
           , "two" : 2
           , "three" : 3];

dict.byPair
    .filter!(x => x[0] == "one")
    .assocArray; 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，我要求通过每个键/值对遍历字典。过滤后，我们建立了一个新的字典。

作为一个题外话，当我在这些中发现一致性时，我也意识到 D 有不一致性。