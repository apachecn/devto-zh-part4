# Ruby 代码基准测试

> 原文：<https://dev.to/scoutapm/benchmarking-ruby-code-3gkc>

*这篇文章最初出现在[童子军博客](https://blog.scoutapm.com)上。*

使用 Ruby 语言的乐趣之一是你可以用许多不同的方法解决同一个问题，这是一种非常有表现力的语言，有丰富的库。但是我们如何知道哪一种是最好的、最有效的语言使用方式呢？当我们讨论对应用程序性能至关重要的算法时，理解最有效的方法是必不可少的。[也许你一直在使用 Scout APM](https://scoutapm.com) 寻找问题，现在你发现了一个问题，你想优化它。Ruby 的基准测试模块为您提供了一个非常方便的方法来比较和对比可能的优化，当与一个好的 APM 解决方案结合使用时，它将确保您覆盖所有的基础。今天就让我们来看看你可以如何开始使用它吧！

## Ruby 的基准测试模块

Ruby 提供了一个很好的叫做 Benchmark 的小模块，足以满足大多数人的需求。它允许您执行代码块并报告它们的执行时间，允许您查看哪种方法是最快的。你所要做的就是像这样包含基准模块:

```
require 'benchmark' 
```

Enter fullscreen mode Exit fullscreen mode

我们将很快介绍几种不同的方法，但是首先，无论您选择哪种方法，Ruby 提供给您的基准测试结果都将以 Benchmark::Tms 对象的形式提供，它们看起来像这样:

```
user        system        total        real

5.305745    0.000026      5.305771     5.314130 
```

Enter fullscreen mode Exit fullscreen mode

第一个数字显示用户的 CPU 时间，换句话说，执行代码花了多长时间，忽略系统级调用。接下来是系统 CPU 时间，它显示了在内核中花费的时间，可能是执行一个系统调用。第三个数字是这两个值的总和，最后一个数字显示了实际的执行时间(如果您对操作从开始到结束进行计时的话)。

## 测量单个代码块

如果您想度量单个代码块的性能，并且对比较多种方法不感兴趣，那么您可以使用 Benchmark::measure 方法。

```
result = Benchmark.measure do

  10000.times { x = "lorem ipsum" }

end

puts result 
```

Enter fullscreen mode Exit fullscreen mode

measure 方法(像本模块中的所有方法一样)以 Benchmark::Tms 格式返回结果，您可以轻松地打印出来。记住代码块可以是任何东西，比如方法调用等等。

## 比较多个代码块

当你想比较一个问题的两种或两种以上不同的方法，以确定哪种方法是最好的方法时，那么你可以使用 Benchmark::bm 方法。这个方法是 Benchmark::benchmark 方法的一个更易于使用的版本。

在下面的代码片段中，我们定义了两个略有不同的方法:一个使用 for 循环，一个使用 times 方法。通过使用 Benchmark::bm，我们可以调用 report 方法，为我们希望进行基准测试的标签和代码块传递一个值。

```
def old_method
  for i in 1..100_000_000
    x = "lorem ipsum"
  end
end

def new_method
  100_000_000.times { x = "lorem ipsum" }
end

Benchmark.bm do |x|
  x.report("Old:") { old_method }
  x.report("New:") { new_method }
end 
```

Enter fullscreen mode Exit fullscreen mode

这是生成的输出:

```
 user        system        total        real

Old:    5.305745    0.000026      5.305771     (  5.314130)

New:    5.084740    0.000000      5.084740     (  5.092787) 
```

Enter fullscreen mode Exit fullscreen mode

在由于垃圾收集开销而导致结果不公平的情况下，提供了另一个类似的方法 Benchmark::bmbm。该方法进行两次基准测试，显示两次测试的输出。正如您在下面的例子中看到的，第二次测试的执行速度更快。

```
Rehearsal ----------------------------------------

Old:   5.470750   0.000000 5.470750 (  5.479510)

New:   5.131210   0.000000 5.131210 (  5.139404)

------------------------------ total: 10.601960sec

       user       system   total    real

Old:   5.432181   0.000000 5.432181 (  5.440627)

New:   5.061602   0.000000 5.061602 (  5.069408) 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

正如你所看到的，Ruby 的基准模块易于使用，它给我们的洞察力非常有用。一旦你熟悉了标准的基准测试模块，你可以用基准测试 ips gem (显示每秒迭代次数信息)或 Readygo gem (采用更独特的基准测试方法)更进一步[。或者，如果你只是想从别人对 Ruby 的许多特性的基准测试中获得回报，](https://github.com/evanphx/benchmark-ips)[，那么这个页面是一个有趣的阅读](https://github.com/JuanitoFatas/fast-ruby)。