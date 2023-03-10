# Perl 每周挑战 15，第 1 部分:用 Perl 寻找强素数和弱素数

> 原文：<https://dev.to/yzhernand/perl-weekly-challenge-15-part-1-finding-strong-and-weak-primes-with-perl-5c65>

本周的 [Perl 每周挑战](https://perlweeklychallenge.org/blog/perl-weekly-challenge-015/)再次触及了素数序列，以及一些老派的密码学(这相当令人兴奋)。这篇文章分为两部分，每个挑战一篇。在 Github 页面的 [dev.to](https://dev.to/yzhernand/perl-weekly-challenge-15-part-2-encrypting-decrypting-with-the-vigenere-cipher-l2) 或 [my blog](https://yzhernand.github.io/posts/perl-weekly-challenge-15) 上找到第 2 部分。

# 挑战 1

> 写一个脚本来生成前 10 个强素数和弱素数。
> 比如第* n *个质数用`p(n)`来表示。
> 
> p(1)= 2
> p(2)= 3
> p(3)= 5
> p(4)= 7
> p(5)= 11
> 
> 强素数`p(n)`当`p(n) > [ p(n-1) + p(n+1) ] / 2`时
> 弱素数`p(n)`当`p(n) < [ p(n-1) + p(n+1) ] / 2`

多亏了之前的挑战，我已经有了一些生成素数的代码。然而，这段代码只允许我遍历一个序列，每次迭代器被调用时生成下一个素数。这个挑战需要存储序列的所有值，或者快速重新生成它们，以便可以测试序列中的多个值。

强素数和弱素数是根据它们与素数序列中的近邻的接近程度来定义的:强素数更接近序列中的下一个数，而弱素数更接近前一个数(见上面的公式)。

在我的第一次尝试中，我选择在每个强/弱牌上生成数字。我使用了 [`Memoize`](https://metacpan.org/pod/Memoize) 模块，这样就不会太贵，节省时间:

```
use v5.24;
use strict;
use warnings;
use feature qw(say state signatures);
no warnings "experimental::signatures";
use Memoize;
use List::Util qw(first);

memoize('prime_gen');

sub is_prime {
    my $n = shift;

    return 0 if grep { $n % $_ == 0 } ( 2 .. sqrt($n) );

    return 1;
}

sub prime_iterator {
    my $n = 1;
    return sub {
        1 until is_prime ++$n;
        return $n;
        }
}

sub prime_gen ($n) {
    my $prime_iter = prime_iterator();

    # Throw away the first n-1 values
    $prime_iter->() for (1 .. ($n-1));
    return $prime_iter->();
}

sub is_strong_prime ($n) {
    return ($n > 1) && prime_gen($n) > ((prime_gen($n-1)+prime_gen($n+1))/2);
}

sub strong_prime_gen ($n) {
    my @strong_primes;
    my $i = 1;

    while (@strong_primes < $n) {
        my $p = prime_gen($i);
        push @strong_primes, $p if is_strong_prime($i);
        $i++;
    }

    return pop @strong_primes;
}

sub is_weak_prime ($n) {
    return ($n > 1) && prime_gen($n) < ((prime_gen($n-1)+prime_gen($n+1))/2);
}

sub weak_prime_gen ($n) {
    my @weak_primes;
    my $i = 1;

    while (@weak_primes < $n) {
        my $p = prime_gen($i);
        push @weak_primes, $p if is_weak_prime($i);
        $i++;
    }

    return pop @weak_primes;
}

say "Strong primes: ", join(", ", map {strong_prime_gen($_)} (1 .. 10));
say "Weak primes: ", join(", ", map {weak_prime_gen($_)} (1 .. 10)); 
```

基本上，我会用序列中我想要的质数的索引来调用`(strong|weak)_prime_gen`函数。这些函数调用质数生成函数`prime_gen`，以及另一个检查数字实际上是强还是弱的函数。那些检查函数也调用了`prime_gen`函数，所以我记住了它。当我写的时候，我觉得我很聪明...

然而，很明显这里有很多问题。首先，虽然清楚地将事情分开，然后按照您可能需要的方式将它们放在一起是很好的，但是大量的代码可以合并在一起——它不会在其他地方重用。另外，`(strong|weak)_prime_gen`函数看起来*和*非常相似。唯一的区别是不等式检查的方向，它隐藏在单独的检查函数中。最后，重复两次相同的质数会浪费时间，因为首先我调用了`strong_prime_gen`函数，然后是`weak_prime_gen`函数。

我可以遍历一个质数序列，同时进行上述必要的检查，并将我想要保存的数字存储在适当的列表中。我也可以有一个质数列表，因为我无论如何都需要它们。这些都只是整数，对于挑战，我知道我只需要 10 个强/弱素数，所以我不介意空间的权衡。

所以现在我只需要考虑案例。当我遍历素数时，我需要检查上面的不等式。我知道我需要在我的质数序列中至少有 3 个元素才能进行这样的比较，所以我们会考虑到这一点(可能是一个`if (@primes > 2)`或什么)。接下来，是比较的结果。一个素数可以大于(强)、小于(弱)或等于(平衡)其近邻的平均值。百米...听起来很像使用`<=>`或`cmp`操作符进行的比较，这两种操作符分别返回 1、-1 或 0。

```
sub is_prime ($n) {
    return 0 if grep { $n % $_ == 0 } ( 2 .. sqrt($n) );

    return 1;
}

sub prime_iterator {
    my $n = 1;
    return sub {
        1 until is_prime ++$n;
        return $n;
    }
}

my ( @primes, @strong_primes, @weak_primes, @bal_primes );
my @which_arr = ( \@bal_primes, \@strong_primes, \@weak_primes );
my $iter      = prime_iterator();
my $n         = 10;

while ( ( @strong_primes < $n ) || ( @weak_primes < $n ) ) {
    push @primes, $iter->();
    if ( @primes > 2 ) {
        push $which_arr[ ( $primes[-2] <=> ( $primes[-3] + $primes[-1] ) / 2 )
        ]->@*, $primes[-2];
    }
}

say "First 10 weak primes: " . join( ", ", @weak_primes );
say "First 10 strong primes: " . join( ", ", @strong_primes ); 
```

哦，这要好得多:少得多的代码，少得多的重复，我甚至不需要记忆。我利用了`<=>`的返回值，制作了一个我需要的列表的引用列表，我称之为`@which_arr`。顺序很重要，因为我使用了`<=>`的返回值作为该数组的索引，所以一个`-1`(小于)结果意味着该值应该进入`@weak_primes`数组，这意味着对它的引用需要是数组的最后一个元素。起初这可能是违反直觉的，但是一个`-1`索引与最后一个索引是相同的，因为负索引从数组的末尾开始。

同样可能违反直觉的是，我们保存的值不是`@primes`序列中的最后一个值，而是倒数第二个值，`$primes[-2]`,因为这是最后一个值，它可以同时具有前一个值和后一个值。

[见完整解，这里](https://github.com/manwar/perlweeklychallenge-club/tree/master/challenge-015/yozen-hernandez/perl5/ch-1.pl)。