# Perl 每周挑战 16:用 Perl 解码比特币地址

> 原文：<https://dev.to/yzhernand/perl-weekly-challenge-16-torturing-your-party-guests-with-the-pythagoreas-pie-puzzle-2dp5>

编辑:标题(叹气)

在我的[自己的博客](https://yzhernand.github.io/posts/perl-weekly-challenge-16-2/)上查看这篇文章，在那里数学文本被正确渲染:)，但是你不能留下任何评论:(。

本周 Perl Weekly Challenge 的第二个挑战与比特币有关，这种无处不在的数字货币我们都听说过，但很少有人完全理解，有些人希望我们当初没有投资。

> 编写一个脚本来验证给定的比特币地址。大多数比特币地址是 34 个字符。它们由随机数字和大小写字母组成，除了大写字母“O”、大写字母“I”、小写字母“l”和数字“0”从不用于防止视觉模糊。一个比特币地址编码 25 个字节。最后四个字节是校验和检查。它们是前 21 个字节的双 SHA-256 摘要的前 4 个字节。更多信息，请参考 [wiki](https://en.bitcoin.it/wiki/Address) 页面。

这种挑战对我来说是新的。我从未处理过 SHA 散列、解码或比较校验和(至少在 Perl 中没有)。我做过一些涉及字节或位计算的编程，但肯定不是用 Perl。我花了几个小时在 wiki 上阅读一些东西，然后才知道我需要做什么。

每个比特币地址都是一个数字，尽管是一个相当大的数字，以 58 为基数编码，这意味着有 58 个字符用于表示值(而 10 为基数，0-9 只有 10 个字符)。字符依次为:

```
123456789ABCDEFGHJKLMNPQRSTUVWXYZabcdefghijkmnopqrstuvwxyz 
```

如挑战中所述，一些字符被省略，因为在某些情况下它们可能会在视觉上相互混淆:大写字母“O”、数字“0”、大写字母“I”和小写字母“l”。

现在，有了什么字符代表什么值的概念，下面是解码和验证比特币地址的大致步骤:

1.  迭代输入地址字符串中的每个符号，转换为乘以符号“位置”的数值表示。因为地址可以更短或更长，所以从右向左迭代。
2.  将该值添加到累计总数中。
3.  迭代后，将数值总计转换为字节表示。
4.  如果地址比最长的有效比特币地址短，则用“0”填充。

看起来很简单，对吧？我还没有看过其他人的博客帖子，但是我遇到了一些问题，我将在下面详述。

正如第 1 点中提到的，我们需要反向迭代字符串，至少为了清晰起见，因为比特币地址最多 34 个字符，但可以更短。从右边开始，让我们从 0 开始一个计数器，它随着字符串的每一次向左移动而增加。该计数器用于将当前字符的值乘以基数的正确幂。如果这让你感到困惑，这就是你如何用十进制数字做同样的解码，这看起来如此自然，你可能没有意识到你正在做这件事(除非你是数学/计算机科学专业的学生，我猜):\(1295 = 1\times10^3+2\times10^2+9\times10^1+5\times10^0\).)

现在来跟踪我们的比特币地址的运行总数。在我的解码功能:

```
 my $val;
    my $base = 58;
    my @btc_arr = reverse split( //, $btc_addr );

    while ( my ( $i, $v ) = each @btc_arr ) {
        croak("Invalid bitcoin address") unless (exists $b58{$v});
        $val += $b58{$v} * ($base**$i);
    } 
```

所以这没用。等等，什么？

当我使用 [`Test::More`](https://metacpan.org/pod/Test::More) 编写函数时，我会测试它们的输出。使用一个在线的[比特币地址解码器](http://lenschulwitz.com/base58)和[打包/解包](https://perldoc.pl/functions/pack)函数(多亏了这些挑战，我才知道这些)，我将我得到的东西与工具的输出进行比较:

```
is(unpack("H*", b58_decode('1BvBMSEYstWetqTFn5Au4m4GFg7xJaNVN2')), lc'0077BFF20C60E522DFAA3350C39B030A5D004E839AF415766B', "b58 decode works"); 
```

`unpack("H*", $blah)`根据模板`"H*"`将`$blah`中的任意字节转换成十六进制字符串(按照[大端](https://en.wikipedia.org/wiki/Endianness)的顺序)。函数的返回值被转换成十六进制，然后与十六进制字符串进行比较，我知道这是解码的十六进制值。我使用小写来转换它，因为 unpack 返回小写字符串。

数值被转换成字节，并在函数中返回，大致如下:

```
 my $decoded;

    # Bytes are 8-bit == 256 values
    while ($val >= 256) {
        # Get next lowest byte
        my $mod = $val % 256;

        # Prefix chr value of $mod to byte string
        $decoded = chr($mod) . $decoded;
        $val = int($val / 256);
    }

    $decoded = chr($val) . $decoded;

    #0-padding
    $decoded = (0 x (25-length($decoded))) . $decoded;
    return $decoded; 
```

你可能会注意到这里实际上有两个问题。但是第一个问题是 Perl 不支持开箱即用的“大”数字。而且比特币地址值确实是*非常*大的数字。好了，我们用 [`Math::BigInt`](https://metacpan.org/pod/Math::BigInt) 。问题解决了，对吧？

```
sub b58_decode ($btc_addr) {
    my $val = Math::BigInt->new();
    my $base = Math::BigInt->new(58);
    my @btc_arr = reverse split( //, $btc_addr );

    while ( my ( $i, $v ) = each @btc_arr ) {
        croak("Invalid bitcoin address") unless (exists $b58{$v});
        $val = $base->copy()->bpow($i)->bmuladd($b58{$v}, $val);
    }

    # Decode value to bytes
    my $decoded = $val->to_bytes();

    # 0-padding
    $decoded = (0 x (25-length($decoded))) . $decoded;
    return $decoded;
} 
```

感谢 Math::BigInt 中的`to_byte`方法，我可以快速地将值转换成字节表示。但这还是没用！是啊，愚蠢的错误...我想要用字节表示，为此你必须使用`chr`函数，而不仅仅是简单的`0` :

```
 $decoded = (chr(0) x (25-length($decoded))) . $decoded; 
```

我的测试终于通过了！现在验证根本不需要太多:

```
sub btc_valid ($btc_addr) {
    my $decoded = b58_decode($btc_addr);

  # The last four bytes are a checksum check. They are the first four bytes of
  # a double SHA-256 digest of the previous 21 bytes.
    my $checksum = substr $decoded, -4;
    my $data     = substr $decoded, 0, 21;

    return ( $checksum eq substr(sha256( sha256($data) ), 0, 4) ) ? substr($decoded, 0, 1) : undef;
} 
```

核心模块 [`Digest::SHA`](https://metacpan.org/pod/Digest::SHA) 提供哈希函数`sha256`。我们对我的解码函数返回的字符串使用`substr`来提取我们想要的字节(这比 split/slice/join'ing 更快)，并将最后 4 个字节与前 21 个字节的双重散列进行比较。

这个函数成功后的返回值来自于我查看[这个线程](https://bitcointalk.org/index.php?topic=1026.0)(由比特币维基提供)以及那里的用户用其他语言提供的解决方案。就个人而言，我认为使用 [`Math::BigInt`](https://metacpan.org/pod/Math::BigInt) 的 Perl 解决方案看起来比那里的解决方案更简洁，但是我已经准备好被其他挑战参与者的答案所震惊。

这当然是一个很难解决的问题，但是它迫使我使用我以前没有用过的大脑和 Perl，这可能会派上用场。

[见完整解，这里](https://github.com/manwar/perlweeklychallenge-club/tree/master/challenge-016/yozen-hernandez/perl5/ch-2.pl)。