# 滥用 Ruby 哈希取乐

> 原文：<https://dev.to/oinak/abusing-ruby-hashes-for-fun-1epe>

每个 Ruby 开发者都喜欢他们的散列，它们在内核中可用， [O(1)](https://en.wikipedia.org/wiki/Big_O_notation#Orders_of_common_functions) (意思是*快*)用于访问，它们包括世界上最棒的模块:[可枚举](https://ruby-doc.org/core-2.6.3/Enumerable.html)。

然而，事实上，我们习惯于仅仅把当作字典，或者方便的数据集合，每当我们懒得创建一个自定义类的时候。

这意味着我们通常只将`"strings"`或`:symbols`作为键。但是哈希键并不局限于此，你可以使用任何响应`.hash`的方法，而且在 ruby 中是*几乎所有的*。

所以考虑到这一点，我开始尝试测试这个想法的局限性:

散列的基本用法，使用布尔值作为键:

```
>> h = {true => '1 is odd', false => '1 is even'}
=> {true => "1 is odd", false => "1 is even"}
>> h[true]
=> "1 is odd" 
```

Enter fullscreen mode Exit fullscreen mode

让我们计算密钥，而不是使用文字值

```
>> h = {1.odd? => '1 is odd', 1.even? => '1 is even' }
=> {true=>"1 is odd", false=>"1 is even"}
>> h[true]
=> "1 is odd" 
```

Enter fullscreen mode Exit fullscreen mode

让我们将`p.odd?`的`1.odd?`改为动态定义:

```
>> p = 1; h = { p.odd? => "#{p} is odd", p.even? => "#{p} is even" }
=> {true=>"1 is odd", false=>"1 is even"}
>> h[true]
=> "1 is odd"

>> p = 0; h = { p.odd? => "#{p} is odd", p.even? => "#{p} is even" }
=> {false=>"0 is odd", true=>"0 is even"}
>> h[true]
=> "0 is even" 
```

Enter fullscreen mode Exit fullscreen mode

但是这只适用于在`h`之前定义`p`，所以让我们更进一步:

```
>> f = -> (p) { { p.odd? => 'odd', p.even? => 'even' }[true] }
=> #<Proc:0x00000002052bf0@(irb):49 (lambda)>
>> f[1]
=> "odd"
>> f[0]
=> "even" 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果来自键的过程可以是更复杂的谓词，比如*嵌套过程* :
会怎么样

```
>> f = -> (p) { { ->(a) { a.odd? }[p] => 'odd', ->(a) { a.even? }[p] => 'even' }[true] }
=> #<Proc:0x0000563ce10524d0@(irb):10 (lambda)>
>> f[1]
=> "odd"
>> f[2]
=> "even" 
```

Enter fullscreen mode Exit fullscreen mode

牙套让你头晕吗？让我们换一种说法

```
>> f = proc do |p|
  Hash.new.merge(
    proc { |a| a.odd?  }.call(p) => 'odd',
    proc { |b| b.even? }.call(p) => 'even'
  ).fetch(true)
end
=> #<Proc:0x00000001fac070@(irb):52 (lambda)>
>> f[1]
=> "odd"
>> f[2]
=> "even" 
```

Enter fullscreen mode Exit fullscreen mode

你可能会告诉我，这是一种非常复杂的方式来告诉一个数字的奇数，但奇数和偶数在这里只是为了不分散解释的注意力，事实是，你可以用适当的参数替换任何接受`.call`的东西，并为任何不相交的离散集提供一个非常精简的分类器。

这不一定是最好的方法，你可以使用 case/when 语句。但至少我希望你今天离开的时候，对 ruby 散列和它们的键上能有什么有一个更广泛的了解。

页（page 的缩写）向*遭受*这种没有解释的白板脑筋急转弯的 [@tholford0](https://twitter.com/tholford0) 致敬。(不是采访)