# Perl 每周挑战 13:本月的最后一个星期五/相互递归方法

> 原文：<https://dev.to/yzhernand/perl-weekly-challenge-13-last-friday-of-the-month-mutually-recursive-methods-73f>

这是我在 dev.to 上的第一篇帖子。通常，我会在 Github Pages 上的博客上发帖，但我想尝试一下在 dev.to 上发帖，这个网站有评论:)。

本周的 [Perl 每周挑战](https://perlweeklychallenge.org)有两个相当有趣的挑战，以及第三个 API 挑战，不幸的是需要一个额外的步骤，我不愿意参加(该网站需要信用卡详细信息才能在任何级别使用他们的 API)。

# 挑战 1

> 编写一个脚本来打印给定年份中每个月的最后一个星期五的日期。

对于这个挑战，我选择走简单的路线，只使用广泛推荐的[日期时间](https://metacpan.org/pod/DateTime)模块。对于有些任务，使用现有的库会更好，因为所有繁重的工作已经为您完成了。处理日期和时间就是这样一种情况。

```
use DateTime;
use DateTime::Duration;

my $year = shift or die "Usage: $0 <year>\n";

my $dt = DateTime->new(
    month => 1,
    day   => 1,
    year  => $year
);
my $add_week = DateTime::Duration->new( weeks => 1 );
my $add_day  = DateTime::Duration->new( days  => 1 ); 
```

在我的解决方案中，我采用了一种相当简单的方法。在上面的块中，我设置了几个变量:年份来自命令行上的用户，`$dt`变量是一个`DateTime`对象，我将使用它作为一种“光标”来跟踪我们在这一年中的位置，为了方便起见，最后两个变量(`$add_week`和`$add_day`)是`DateTime::Duration`对象。我将使用这两个变量来“遍历”一年中的日期。下面你就明白我的意思了。

```
while ( $dt->day_of_week() != 5 ) { $dt->add($add_day); } 
```

在我前面展示的设置块中，`$dt`被初始化为给定年份的第一个日期，即 1 月 1 日。只要`$dt`中的当前日期不是星期五，这个块中的第一个 while 循环就会增加(使用 [`DateTime::add`](https://metacpan.org/pod/DateTime#%24dt-%3Eadd(-parameters-for-DateTime::Duration-)) )一天。因此，如果 1 月 1 日不是星期五，继续增加一天，直到我们到达一个星期五。如果是的话，我们就不加任何天数。

这就是`$add_day`变量的用途。`DateTime`模块提供了调用该函数的另一种方式，允许我们给出创建新的`DateTime::Duration`对象所需的相同参数(例如，`$dt->add(days => 1)`)。这种形式的调用在每次调用时都会创建一个新的`DateTime::Duration`对象，这就是为什么我决定避免它，而是保留一个已经初始化的变量来实现这个目的。即使一些内部优化足够聪明，可以消除任何潜在的减速，我仍然喜欢明确地说明我在代码中做了什么。

```
my %last_fri;
while ( $dt->year == $year ) {
    $last_fri{ $dt->month } = $dt->ymd("/");
    $dt->add($add_week);
} 
```

现在我们按周进行(使用`$add_week`，与上面使用变量的动机相同)。我使用一个 hash 并以“y/m/d”格式保存日期的字符串表示，使用月份的数值作为键。这带来了一个好的副作用，即只保存这个月的最后一个星期五，因为只要这个月是相同的，以前的星期五就会被覆盖，这就是挑战要求我们做的。

当我们进入下一年时，我们退出循环。

最后，我们打印结果:

```
{
    local $, = "\n";
    say @last_fri{ sort { $a <=> $b } keys %last_fri };
} 
```

上面可能是一行程序，但是我想利用一个可能不是每个人都知道的特性。也就是本地的`$,`变量(也叫`$OFS`)的设置。花括号用于开始一个新的块，我们使用`local`关键字告诉 perl 我们想要使用一个现有的变量名，但是我们将对它做一个本地拷贝，这样我们就可以安全地在这个块中改变它的值。现在，用`say`打印会用“\n”分隔它的每个参数(通常，`$,`被设置为`undef`)。注意，上面的内容可以通过一个简单的使用“\n”作为分隔符的`join`来实现，但是如果我们需要使用相同的分隔符打印几个列表，并且希望节省击键次数，那么上面的表单会很方便。

我对我的 hash 的键进行数字排序，以便按照挑战中指定的方式打印出日期。

# 挑战 2

> 写一个脚本来演示相互递归的方法。如果第一个方法调用第二个方法，而第二个方法依次先调用
> ，那么这两个方法就是
> 相互递归的。使用相互递归的方法，生成[霍夫斯塔德女性和男性序列](https://en.wikipedia.org/wiki/Hofstadter_sequence#Hofstadter_Female_and_Male_sequences)。

递归方法写起来很有趣，但是我从来没有做过相互递归的方法。这是一个看似简单的挑战。一旦我写了它，我有点想自己“这不可能是全部，对不对？”。所以，当然，我稍微活跃了一下

```
use feature qw(say state);

sub hofstadter_F {
    my $n = shift;
    state %cache = ( 0 => 1 );

    return $cache{$n} if exists $cache{$n};
    return $cache{$n} = $n - hofstadter_M( hofstadter_F( $n - 1 ) );
}

sub hofstadter_M {
    my $n = shift;
    state %cache = ( 0 => 0 );

    return $cache{$n} if exists $cache{$n};
    return $cache{$n} = $n - hofstadter_F( hofstadter_M( $n - 1 ) );
} 
```

首先要注意的是，每个函数的主体都有对其他函数的调用。这种事情就是管用。因此...我们就到此为止吧。

更有趣的是使用了 hash，我毫无创意地称之为`%cache`,因为它是...一个宝藏！如果没有这个缓存，我们必须在每次调用时计算函数的中间值。为什么这很重要？

霍夫施塔特的女性和男性函数产生序列，这意味着我们可能需要调用它们 *n* 次来获得第一个 *n* 项。如果我们调用它们 10 次，您可能不会注意到任何性能改进的需要。但是如果您想要前 100 个值的序列，您会注意到您需要等待一段时间。

输入`state`，我们使用`use feature 'state';`将其启用。类似于我在[之前的博文](https://yzhernand.github.io/posts/perl-weekly-challenge-12/)中谈到的闭包，`state`是一种在函数调用中保持变量状态的方式。然而，`state`允许我们在一个命名函数中保存一个变量的状态，而不需要保存另一个对它的引用，而不是返回一个保持自己状态的匿名 sub。

在每个函数中，我初始化缓存散列来包含基本调用的值(`F(0)`或`M(0)`)。然后，在每次调用时，检查散列以查看我们是否已经计算了序列中第* n *项的值。如果是，则返回。否则，我们进行适当的调用来计算它，并将它保存到缓存中，同时也返回它。

```
my $n = shift or die "Usage: $0 <n>\n";

say "F: ", join( ", ", map { hofstadter_F($_) } ( 0 .. $n ) );
say "M: ", join( ", ", map { hofstadter_M($_) } ( 0 .. $n ) ); 
```

在我的机器上，在不使用缓存的情况下，用`$n = 100`调用这两个函数总共需要将近 31 秒的运行时间！有了缓存，程序运行时间不到 40 毫秒，一眨眼就完成了。把它提高到 10000 用了 90 毫秒。在没有缓存情况下，我没有尝试过 *n* 的值。

在特定条件下，一个名为 [`Memoize`](https://metacpan.org/pod/Memoize) 的核心模块会为您进行这种缓存。POD 页面非常值得一读。我本来可以通过使用`Memoize`来完成上述工作，但那样我可能就没有机会提供一个例子来写了:)。