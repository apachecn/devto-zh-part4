# Perl 每周挑战 14: van Eck 在过度思考中的顺序/冒险——从美国州首字母造词

> 原文：<https://dev.to/yzhernand/perl-weekly-challenge-14-van-eck-s-sequence-adventures-in-overthinking-making-words-from-us-state-initials-43n9>

**警告**这篇文章最后变得很长。如果你能坚持到最后，万分感谢。

https://yzhernand.github.io/posts/perl-weekly-challenge-14/因为数学文本在这里表现不好。

本周的 [Perl 每周挑战](https://perlweeklychallenge.org/blog/perl-weekly-challenge-014/)提供了两个非常好的挑战，我真的不得不思考。本周晚些时候，建议发起第二次挑战的尼尔·鲍尔斯(Neil Bowers)，[发布了](http://neilb.org/2019/06/24/additional-challenge-14.html)关于一次额外的挑战，因为他的提交被误解了。

现在，让我告诉你我是如何应对这些挑战的。

# 挑战 1

> 写一个脚本生成 Van Eck 的序列从 0 开始。更多信息，请查看维基百科[页面](https://en.wikipedia.org/wiki/Van_Eck%27s_sequence)。这个挑战是团队成员 Andrezgz 提出的。

又是一周，又是一个数字序列:)。van Eck 的序列比我在以前的挑战中生成的其他序列稍微复杂一点(见我在[挑战 9](https://yzhernand.github.io/posts/perl-weekly-challenge-9/) 中简单生成方块的帖子，[挑战 12](https://yzhernand.github.io/posts/perl-weekly-challenge-12/) 讨论[欧几里德数](https://en.wikipedia.org/wiki/Euclid_number)，以及[挑战 13](https://yzhernand.github.io/posts/perl-weekly-challenge-13/) 讨论[霍夫施塔特女性和男性序列](https://en.wikipedia.org/wiki/Hofstadter_sequence#Hofstadter_Female_and_Male_sequences))，我们将会看到。

```
sub van_eck_seq ( $n, $init = 0 ) {

    # Base case. It should always be $init followed
    # by 0, given the definition.
    return ( $init, 0 ) if ( $n == 0 );

    my @seq = van_eck_seq( $n - 1, $init );
    my $m   = first { $seq[$_] == $seq[$n] } reverse( 0 .. ( $n - 1 ) );
    my $val = ( defined $m ) ? ( $n - $m ) : 0;

    return @seq, $val;
} 
```

根据维基百科上对序列的定义，

> \(a_0 = 0\)。然后，对于\(n ≥ 0\)，如果存在一个\(m < n\)使得\(a_{m} = a_n\)，取最大的这样的\(m\)，设\(a _ { n+1 } = n m \)；否则\(a_{n+1} = 0\)。

我认为这是“每第(n)项产生第(n+1)项”。所以，如果你用`van_eck_seq(2)`调用我上面写的函数，你将得到序列的前 3 个元素，而不是前 2 个。这一点很重要，因为我已经将函数编写为递归的，嵌套调用之后的代码期望它返回的列表包含“当前的”\(n
\)项。

同样根据这个定义，它看起来像\(a_1 = 0\ ),因为从来没有一个\(m < n\)使得\(a_{m} = a_n\)。因此，`van_eck_seq(0)`的基本情况是返回一个包含项目`(0, 0)`的列表。

实际上，这并不完全正确:van Eck 的序列可以有一个不同的\(a_0\)项，它改变了序列。所以我的函数定义允许可选地设置该项作为它的第二个参数，`$init`。

接下来，对于 all \(n \geq 1\)，我们对同一个函数进行嵌套调用，向它传递`$n-1`和`$init`的值(以防调用者更改它)，并使用返回的序列生成下一个值。

这行:

```
my $m   = first { $seq[$_] == $seq[$n] } reverse( 0 .. ( $n - 1 ) ); 
```

让我们回顾一下从嵌套调用中得到的序列，这样我们就可以找到\(m < n\) mentioned in the definition, if any. The value of 【T0】 depends on whether or not such an \(m\) was found, and then the combined sequence is returned. The function can be called like this:

```
local $, = ", ";
say van_eck_seq(10); 
```

其输出为:

```
0, 0, 1, 0, 2, 0, 2, 2, 1, 6, 0, 5 
```

对于这个挑战，我使用了`Memoize`模块，而不是缓存我自己(见我的[帖子](https://yzhernand.github.io/posts/perl-weekly-challenge-13/)关于上周的挑战)。我还记录了函数调用返回 n+1 个元素的列表的行为，因为想要使用该函数的人可能不会想到这一点。

[见完整解，这里](https://github.com/manwar/perlweeklychallenge-club/tree/master/challenge-014/yozen-hernandez/perl5/ch-1.pl)。

# 挑战 2

> 仅使用美国 50 个州的官方邮政(两个字母)缩写，编写一个脚本，找出您能拼写的最长的英语单词？这是维基百科页面上的美国各州缩写列表。这项挑战是由团队成员尼尔·鲍尔斯(Neil Bowers)提出的。

事实证明，这个挑战并不是尼尔想要的，但我认为我们大多数参与者都会照原样解决它。接下来，我写下了我对他实际上有意挑战的解决方案。

```
my %longest_word = ( words => [], length => 0 );

#...
open my $wl, "<", "/usr/share/dict/words";
while ( my $w = <$wl> ) {

    # Chomp and drop apostrophes or any other
    # non-alphabetical characters
    chomp $w;
    my $w_alpha = $w;
    $w_alpha =~ s/[^[:alpha:]]//g;

    # Skip unless length is even: all US state codes are bigrams
    next unless ( length($w_alpha) % 2 == 0 );

    # Use unpack to split word into an array of bigrams
    # and use UC to make it all uppercase
    my @w_split = unpack "(a2)*", uc($w_alpha);

    # Skip if the set created from the word is not a
    # proper subset of the US state codes list.
    next unless all { exists $us_states_to_val{$_} } @w_split;

    # By now, we can be sure that the word is only
    # composed of elements in the us_states list.
    if ( length($w_alpha) > $longest_word{length} ) {
        @longest_word{qw(words length)} = ( [$w], length($w) );
    }
    elsif ( length($w_alpha) == $longest_word{length} ) {
        push $longest_word{words}->@*, $w;
    }
} 
```

我们从美国各州的列表(未示出)开始，并使用散列来存储所看到的最长单词的长度和那些单词的列表，因为可能有不止一个单词。长度值被初始化为 0。

我们跳过所有奇数长度的字符串，因为美国州代码都是 2 个字母长(二元模型)。我第一次尝试使用`unpack`,因为这看起来是从字符串生成二元模型列表的好方法。那么我们拒绝任何不在美国州代码列表中的单词。

代码的最后一部分只是一个 if-elsif 分支，它检查我们找到的单词是否比目前看到的最长的单词长。如果是，我们基本上重置哈希并存储单词和新的最长长度。如果是相同的，那么我们将这个词放到散列的“words”值中的 arrayref 上。较短的单词会被忽略。

完成后，我看了看这个挑战的其他解决方案。我想我更喜欢其中的一些，因为它们更简洁，但我也喜欢把事情分解，这样我就能更容易地看到每件事都在做什么。我怀疑这两种解决方案之间的性能是相似的，但是我没有进行任何基准测试来检查。

对于 Arch Linux 上的单词列表字典，这个脚本给出了以下输出:

```
Longest word(s) constructed using USPS state codes: armorial, calamari, calamine, coalmine, Concorde, Denmark's, Ganymede, landmine, mainland, malarial, Mandarin, mandarin, melamine, memorial, mescalin, moorland
with a length of 8 alphabetical characters 
```

并且使用来自 https://github.com/dwyl/english-words 的字母列表

```
Longest word(s) constructed using USPS state codes: cacogalactia
with a length of 12 alphabetical characters 
```

[见完整解，这里](https://github.com/manwar/perlweeklychallenge-club/tree/master/challenge-014/yozen-hernandez/perl5/ch-2.pl)。

# 附加挑战 2

> 穿越美国各州，在不重访任何州的情况下，取各州的首字母或缩写，你能拼出最长的单词是什么？

好吧，这让我有点抓狂。我有点嫉妒别人的解决方案，他们看起来比我的简单得多。但是我也为过度工程感到自豪，因为我在计算机科学中使用了一些概念，这些概念从我本科毕业后就没有真正使用过。

当我第一次阅读问题陈述时，我立即想到了一个[图](https://en.wikipedia.org/wiki/Graph_(discrete_mathematics))。基本上，图是一种数据结构，其中事物(通常是字符串或其他某种对象)以某种方式与其他事物相联系。“物”称为“顶点”(sing，vertex)，连接称为“边”。图可以有带方向或不带方向的边，使得图的遍历根据图的类型而不同。幸运的是，Perl 中有一个模块 [`Graph`](https://metacpan.org/pod/Graph) ，可以表示这些结构。

这就满足了各州必须相邻的要求。搜索单词呢？在前面的挑战中，我们可以遍历字典中的所有单词，检查它们是否可以使用州代码。在这里，我们实际上需要根据状态生成单词，给定它们的相邻关系，然后检查这是否是一个有效的单词。

幸运的是，有一种数据结构可以做到这一点: [Trie](https://en.wikipedia.org/wiki/Trie) ,一种允许通过前缀查找单词的搜索树。它是预测文本等应用程序的理想选择，在这些应用程序中，根据到目前为止键入的前几个字母向用户建议单词。我们可以用它来检查我们从图中生成的单词是否在字典中。当然，还有一个 Perl 模块！ [`Tree::Trie`](https://dev.touse%20Tree::Trie) 。

```
sub get_paths ( $graph, $trie, $vertex, $data, %seen ) {
    $seen{$vertex} = 1 + ( keys %seen );

    my $string_so_far = fc(
        join( "",
            map { $data->{$_}->{initials} }
                ( sort { $seen{$a} <=> $seen{$b} } keys %seen ) )
    );

    # Filter out successive vertexes which have already been visited
    # and which would not produce a word in the trie
    my @pot_successors = grep {
        !( exists $seen{$_} )
            && $trie->lookup(
            $string_so_far . fc( $data->{$_}->{initials} ) );
    } sort { $a cmp $b } $graph->neighbors($vertex);

    # Base case: no more successors, done with this path
    if ( !@pot_successors ) {
        return ( join( "->", sort { $seen{$a} <=> $seen{$b} } keys %seen ),
            $string_so_far );
    }
    else {
        # Process successors
        return
            map { get_paths( $graph, $trie, $_, $data, %seen ) }
            @pot_successors;
    }

} 
```

这里我没有展示初始化图或树的代码。您可以查看源代码。然而，上面的函数让我们在给定一个起点/顶点的情况下，在图中进行几次旅行。我们使用一个 hash，`%seen`来跟踪到目前为止沿着路径访问过的顶点。每次访问一个顶点时，它都会进入哈希表，并根据到目前为止的路径长度获得一个值。每次调用这个函数对散列排序时，我都要付出代价，但我愿意这样做。

下一段代码给了我们一个潜在的下一个要访问的顶点的列表，通过删除已经访问过的顶点，并检查访问那个顶点时我们是否仍然在构建一个有效的单词。如果走那条路是一条“死胡同”，那么我们就不会去那个顶点。

当不再有潜在的连续节点时，我们就到达了路径的末端，并构建了两个字符串:所采用的路径和生成的字符串。前者只是为了让我们的输出更好，信息更丰富。后者才是我们真正想要的。这些字符串作为两个元素的列表返回，可以被第一个调用者强制转换成一个散列。密钥冲突应该不是问题，因为所有的路径都是不同的。

如果有更多的节点，我们进行递归调用，从下一个要访问的潜在顶点开始。

**注意:**`%seen`hash 不是 hash ref 的事实使得这一切相对容易:对这个函数的每次连续调用都可以对 hash 做任何想要的修改。这些更改将只存在于该调用的副本中。因此，如果我们在第一个顶点上迭代其他潜在的顶点，我们可以深入一条路径，当我们移动到另一个顶点时，对该函数的下一次调用将获得一个仅包含第一个顶点的`%seen`散列的副本。对于其他连续的呼叫也是如此。

```
# Use a hash to save data on the longest word(s) found.
my %longest_word = ( paths => [], words => [], length => 0 );

# Iterate over all states in the graph.
for my $state ( sort $us_graph->vertices ) {

    # Finds all paths with strings that may be in the dictionary and
    # save them into a hash.
    my %paths = get_paths( $us_graph, $eng_words, $state, \%us_adj_data );

    # Set the trie to do exact string search only
    $eng_words->deepsearch("exact");

    # Iterate over all the path->word elements in the %paths hash
    while ( my ( $path, $search ) = each %paths ) {

        # Drop current path from the hash
        delete $paths{$path};

        # Do an exact string search for the word
        my $match = $eng_words->lookup($search);
        if ( $match && $match eq $search ) {

            # A word was found in the dictionary and its longer
            # than the longest seen so far. Clear the longest_word
            # hash and save the new data.
            if ( length($search) > $longest_word{length} ) {
                @longest_word{qw(paths words length)}
                    = ( [$path], [$match], length($search) );
            }
            elsif ( length($search) == $longest_word{length} ) {

                # A word of the same length as the longest seen
                # was found. Push the data onto the list members
                # of the longest_word hash.
                push $longest_word{paths}->@*, $path;
                push $longest_word{words}->@*, $match;
            }

            # last;
        }
    }

    # Change search setting again so we can do prefix searches on the
    # next iteration again.
    $eng_words->deepsearch("boolean");
} 
```

天啊，这东西真多。不过，这些评论应该会有所帮助，我会指出重要的部分。

上面的代码遍历图中的所有状态。对于每个状态，我们使用`get_paths`函数来获取所有的路径和生成的单词。线

```
$eng_words->deepsearch("exact"); 
```

设置搜索 trie 只查找精确匹配。看起来这个功能并没有像预期的那样工作，但是我需要更多的时间来测试它，并且可能会报告一个错误。无论如何，这意味着我们可以检查我们生成的单词是否真的是有效单词，而不仅仅是字典中某个单词的有效*前缀*。如果我们确实找到了一个精确的匹配，我们以与最初的挑战 2 相似的方式保存它，除了现在我们也保存路径。

在进入下一个状态之前，我们将搜索设置回“boolean ”,如果前缀在 trie 中，则返回“true”。

**注 2:** 在挑战 2 中，我允许匹配带撇号的单词，但由于某种原因，在使用 trie 时，这导致了一些不稳定的输出。为了迎接额外的挑战，我从字典中删除了这些单词。

我很肯定我想多了，设计多了，但是这样做很有趣...一旦我克服了尝试使用 [`Graph::Traversal`](https://metacpan.org/pod/Graph::Traversal) 的挫败感。它没有做到我想要的，上面的`get_paths`是我的解决方案。不管怎样，这最终会变得更好，因为我可以控制不走哪条路。

对于 Arch Linux 上的单词列表字典，这个脚本给出了以下输出:

```
Longest word(s) constructed using initials of US states: 
CO->OK->NM->AZ->NV = conman
with a length of 6 alphabetical characters 
```

并且使用来自 https://github.com/dwyl/english-words 的字母列表

```
Longest word(s) constructed using initials of US states: 
CA->AZ->NV->UT->CO->KS = canuck
MO->AR->LA->MS->AL->GA = malmag
with a length of 6 alphabetical characters 
```

**缺点:**除了是一个过于复杂的解决方案之外，如果它非常大的话，构建 trie 可能需要一段时间。我确信一个足够大的图也会是一个问题。

[见完整解，这里](https://github.com/manwar/perlweeklychallenge-club/tree/master/challenge-014/yozen-hernandez/perl5/ch-2a.pl)。