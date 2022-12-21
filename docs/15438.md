# 在 Ruby 中使用 hash 还是 case 语句？

> 原文：<https://dev.to/jetrockets/use-hash-or-case-statement-in-ruby-ema>

通常，当我们需要基于另一个值获得一个值时，我们使用 case 语句。像这样

```
def realizing_trade_type(realizable_trade_type)
  case realizable_trade_type
  when 'buy'
    'sell'
  when 'short'
    'cover'
  when 'buy_contract'
    'sell_contract'
  when 'short_contract'
    'cover_contract'
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

但是，如果条件和结果都是简单的值，我们为什么不用 hash 呢？我们可以:)

```
REALIZING_TRADE_TYPES = {
  'buy'            => 'sell',
  'short'          => 'cover',
  'buy_contract'   => 'sell_contract',
  'short_contract' => 'cover_contract'
}.freeze 
```

Enter fullscreen mode Exit fullscreen mode

下面是两个选项的基准，执行了 10_000_000 次。它表明，对于这种用途，散列在时间上更快。

```
>> require 'benchmark'
true
>> Benchmark.bm(15) do |x|
  x.report('hash') { 10_000_000.times { REALIZING_TRADE_TYPES['buy'] } }
  x.report('case-statement') { 10_000_000.times { realizing_trade_type 'buy' } }
  x.report('empty') { 10_000_000.times {} }
end
                      user     system      total        real
hash 0.990423   0.003412   0.993835 (  1.057612)
case-statement    1.752263   0.004531   1.756794 (  1.762030)
empty             0.380810   0.000728   0.381538 (  0.382153) 
```

Enter fullscreen mode Exit fullscreen mode

因此，当您只是检索一些值时，最好使用散列(如上例所示)。如果有额外的逻辑要执行，case 语句仍然是一个不错的选择。