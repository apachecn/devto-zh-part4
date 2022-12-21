# Ruff，一个更有效的 Ruby 代数效果库

> 原文：<https://dev.to/nymphium/ruff-more-efficient-algebraic-effects-implementation-library-for-ruby-2nhm>

我一直在为 Ruby 开发一个更高效的代数效果库。

[https://github.com/nymphium/ruff](https://github.com/nymphium/ruff)

已经有一些红宝石特效库，[干特效](https://github.com/dry-rb/dry-effects)和[特效](https://github.com/digital-fabric/affect)。所以我介绍我们图书馆的优点。

# 优点 1:精炼的语法

虽然哪个库提供了更好的语法是一个习惯的问题，但是更好的语法有时有助于避免错误，并且对于初学者来说是值得庆幸的。

与[干效果](https://github.com/dry-rb/dry-effects)相比，我们有更容易理解的语法。

这是从[到](https://github.com/dry-rb/dry-effects/blob/master/examples)的“干效应”示例。

```
class Operation
  include Dry::Effects.State(:counter)

  def call
    3.times do
      self.counter += 1
    end

    :done
  end
end

class Wrapper
  include Dry::Effects::Handler.State(:counter)

  def initialize
    @operation = Operation.new
  end

  def call
    with_counter(0) { @operation.call }
  end
end

Wrapper.new.call # => [3, :done] 
```

即使现在要和上面比较，我也不明白他们想做什么🤔

我们可以更直观地用效果处理程序来编写状态操作。

```
Put = Ruff.instance
Get = Ruff.instance

def with_counter(init, &task)
  state = init

  Ruff.handler
    .on(Put){|k, s| state = s; k[0] }
    .on(Get){|k| k[state] }
    .run &task
end

puts with_counter(0) {
  3.times{|x|
    Put.perform x
    puts Get.perform
  }

  "returns #{Get.perform}"
}

=begin
# ==>
0
1
2
returns 2
=end 
```

# 功绩二:一等延续

代数效果和处理程序最初采用延续，其余的处理计算。
影响说:

> 注意:Affect 并不假装是代数效果的完整的、理论上正确的实现。情感集中在效果背景的概念上。它不处理延续、异步或任何其他并发结构。

我明白了，但我认为操纵延续是代数效果和处理程序的基本能力。
不同于感动，我们有一流的延续。

```
Defer = Ruff.instance

with_defer = Ruff.handler.on(Defer){|k, f|
    k[]
    f[]
  }

with_defer.run {
  Defer.perform(->(){ puts "world" })
  puts "hello"
}

# ==> hello
# ==> world 
```

不错！因为我们可以操作 continuation，所以我们在调用 continuation 后粉碎 thunk。

甚至我们可以在 handler 之外使用 continuation。

```
class Coroutine
  Yield = Ruff.instance

  def self.yield v
    Yield.perform v
  end

  def initialize &task
    @th = ->(_) {
      Ruff.handler.on(Yield){|k, v|
        @th = k
        v
      }.run &task
    }
  end

  def resume *v
    @th[*v]
  end
end

co = Coroutine.new {
  puts "hello"
  x = Coroutine.yield 3
  puts "world"
  puts x + 5
}

v = co.resume nil
co.resume v + 4 
```

# 限制和技术观点

我们有一个限制，您最多只能运行一次延续。
这个限制是因为我们的嵌入方法。
我们用`Fiber`来实现在 Ruby 上嵌入代数效果 <sup id="fnref1">[1](#fn1)</sup> 。
简而言之，在我们的嵌入中，延续对应于协程线程的其余部分。
`Fiber`没有复制其余计算的方法，因此出现了限制。

但你不必失望。甚至在上面我们已经写了一些强大的程序。并且我们可以认为 Affect 的隐式延续是仅在尾部位置运行延续，恰好一次。
[多核 OCaml](https://github.com/ocaml-multicore/ocaml-multicore) 也有代数效果和一击限制的处理程序<sup id="fnref2">T5】2</sup>。

为什么`Fiber`没有方法？我认为，协程被认为是*轻量级*线程，而克隆协程是*重型*程序，所以这不符合概念*轻量级*并被拒绝。
或者实现者可能认为自从 Conway 在 20 世纪 60 年代早期的想法以来，没有必要克隆协程。因此，如果你克隆一个协程线程，我们可以多次运行 continuation。

# 总结

我一直在为 Ruby 开发一个代数效果库，与现有的库相比有很多优点。即使是关于延续的限制，你也可以使用强大的控制构造。

享受代数效果！

# 相关作品

我们已经实现了 [eff.lua](https://github.com/nymphium/eff.lua) ，一个 lua 的代数效果库。
也是基于我们的嵌入方法。

* * *

1.  我们研究了使用栈式非对称协程嵌入单次代数效应。看[这里](https://nymphium.github.io/2018/12/09/asymmetric-coroutines%E3%81%AB%E3%82%88%E3%82%8Boneshot-algebraic-effects%E3%81%AE%E5%AE%9F%E8%A3%85.html)和[这里](http://logic.cs.tsukuba.ac.jp/~sat/pdf/pro2019-01.pdf)(仅限日语)。 [↩](#fnref1)

2.  他们有`Obj.clone_continuation`来克隆延续和运行多次，但是在运行时这是昂贵的，所以他们提供显式克隆。 [↩](#fnref2)