# 具有索引的 while 循环

> 原文：<https://dev.to/smonff/while-loops-that-have-an-index-56h2>

Perl5 语法允许使用 while 循环，而不必通过执行`i++`来显式增加索引。这是通过[功能](https://perldoc.pl/functions/each)实现的。

让我们用一个简单的测试来证明这一点，该测试检查 and 数组和 array ref 包含相同的内容:

```
# t/01_foo_order.t 
use v5.18;
use Test::More tests => 3; 

my $events_arr_ref = get_events();
my @expected_events = ('foo', 'bar', 'baz');

while ( my ( $i, $event ) = each( @$events_arr_ref )) {
  is @$events_arr_ref[$i], 
    $expected_events[$i], 
    "Array element [ $i ] is $expected_events[$i]";
}

done_testing();

sub get_events {
  return [ 'foo', 'bar', 'baz' ];
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们执行我们的测试:

```
prove -v t/01_foo_order.t
1..3
ok 1 - Array element [ 0 ] value is foo
ok 2 - Array element [ 1 ] value is bar
ok 3 - Array element [ 2 ] value is baz
ok
All tests successful.
Files=1, Tests=3,  0 wallclock secs ( 0.03 usr  0.00 sys +  0.07 cusr  0.00 csys =  0.10 CPU)
Result: PASS 
```

Enter fullscreen mode Exit fullscreen mode

`while ( my ( $i, $event ) = each( @$events_arr_ref )) {}`可以迭代`$events_arr_ref`数组引用，对于找到的每个元素，用正确的值初始化`$i`和`$event`。

这与一个`for`循环非常相似，除了你不需要增加索引，而且如果你想迭代整个数组，必须使用它。

我经常用它，如果你想避免的话，可以帅一点。又一个 [TIMTOWTDI](https://en.wikipedia.org/wiki/There%27s_more_than_one_way_to_do_it) ...

**来源**:

*   [*在 StackOverflow 上自动获取 perl 中 foreach 循环的循环索引*](https://stackoverflow.com/questions/974656/automatically-get-loop-index-in-foreach-loop-in-perl)
*   [Perl5 文档为 *`each`函数*](https://perldoc.pl/functions/each)