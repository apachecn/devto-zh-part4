# Perl 每周挑战 17:用 Perl 编写我们自己的 URL 解析器(但是我们应该这样做吗？)

> 原文：<https://dev.to/yzhernand/perl-weekly-challenge-17-writing-our-own-url-parser-in-perl-but-should-we-20mm>

本周 [Perl 每周挑战](https://perlweeklychallenge.org/blog/perl-weekly-challenge-017/)中的第二个挑战要求我们解析一个 URL:

> 创建一个脚本来解析 URL 并打印 URL 的组成部分。根据 [Wiki 页面](https://en.wikipedia.org/wiki/URL)，URL 语法如下:
> 
> **方案:[//[userinfo @]主机[:port]]路径[？查询][#片段]**
> 
> 比如:**JDBC:MySQL://user:password @ localhost:3306/PwC？profile=true#h1**

```
scheme:   jdbc:mysql
userinfo: user:password
host:     localhost
port:     3306
path:     /pwc
query:    profile=true
fragment: h1 
```

听起来不错。让我们试着分析一下。也许我们会使用一些正则表达式，就这样。

```
sub parse_url_regex($url) {
    my %parsed;

    while ($url) {
        if ( !exists $parsed{scheme} ) {
            $url =~ s!^((?:[[:alnum:]]+:)?[[:alnum:]]+):!!;

            # MUST start with the scheme, format: "scheme:"
            croak "Invalid format: url must start with scheme." unless $1;
            $parsed{scheme} = $1;

            return \%parsed unless $url =~ s!^//!!;
        }
        elsif ( !exists( $parsed{host} )
            && $url
            =~ s!^(?:((?:[\d\w]+:)?(?:[\d\w]+)?)@)?([\d\w\.]+)(?::([\d]+))?!!u
            )
        {
            $parsed{userinfo} = $1 // "";
            $parsed{host} = $2 // "";
            $parsed{port} = $3 // "";
        }
        elsif ( !exists( $parsed{path} )
            && $url =~ s!^/((?:[\d\w\.\/]*)+)!!u )
        {
            $parsed{path} = "/" . ( $1 // "" );
        }
        elsif ( !exists( $parsed{query} )
            && $url =~ s!^\?((?:[\d\w\[\]%\"\']+)=(?:[\d\w\[\]%\"\']+))*!!u )
        {
            $parsed{query} = $1 // "";
        }
        elsif ( !exists( $parsed{fragment} )
            && $url =~ s!^#([\d\w\[\]%\"\']+)!!u )
        {
            $parsed{fragment} = $1 // "";
        }
        else {
            croak "Error: Invalid URL? $url";
        }
    }

    return \%parsed;
} 
```

嗯，这是很多的`(els)if`和正则表达式。我不确定我想保持这种状态。另外，我肯定里面全是虫子。

```
sub print_parsed ($url_hash_ref) {
    for my $part (qw(scheme userinfo host port path query fragment)) {
        say "$part:\t" . $url_hash_ref->{$part}
            if exists $url_hash_ref->{$part}
            && defined $url_hash_ref->{$part};
    }
    say "";
}

print_parsed(
    parse_url_regex(
        q"jdbc:mysql://user:password@localhost:3306/pwc?profile=true#h1")
);
#scheme: jdbc:mysql 
#userinfo:       user:password
#host:   localhost
#port:   3306
#path:   /pwc
#query:  profile=true 
#fragment:       h1
print_parsed(
    parse_url_regex(q"http://sri:foo@example.com:3000/foo?foo=bar#23") );
#scheme: http
#userinfo:       sri:foo
#host:   example.com
#port:   3000
#path:   /foo
#query:  foo=bar
#fragment:       23
print_parsed( parse_url_regex(q"https://example.com/") );
#scheme: https
#userinfo:
#host:   example.com
#port:
#path:   /
print_parsed(
    parse_url_regex(
        q"http://JP 納豆.例.jp:80/dir1/引き割り.html?q=クエリ#メイン"
    )
);
#scheme: http
#userinfo:
#host:   JP 納豆.例.jp
#port:   80
#path:   /dir1/引き割り.html
#query:  q=クエリ
#fragment:       メイン 
```

这些看起来都没错。但是肯定有错误。首先，空白字段可能应该保留为`undef`(就像`userinfo`)，但是现在我把它们保留为空字符串。更严重的是，这甚至没有试图确保安全，我不建议在生产中使用它。

如果我在生产中需要这样做的话，这种问题我宁愿用一个已经很好建立的模块来解决。因此，让我们使用一个我知道能很好完成这项工作的模块， [`Mojo::URL`](https://mojolicious.org/perldoc/Mojo/URL) :

```
use Mojo::URL;
use Mojo::Util qw(decode url_unescape);

use Test::More tests => 7;
my $parsed_regex
    = parse_url_regex(
    q"http://JP 納豆.例.jp:80/dir1/引き割り.html?q=クエリ#メイン"
    );
my $parsed_mojo
    = Mojo::URL->new(
    q"http://JP 納豆.例.jp:80/dir1/引き割り.html?q=クエリ#メイン"
    );
is( $parsed_mojo->scheme,
    $parsed_regex->{scheme},
    "Mojo and regex sub agree on 'scheme'"
);
is( $parsed_mojo->userinfo // '',
    $parsed_regex->{userinfo},
    "Mojo and regex sub agree on 'userinfo'"
);
is( $parsed_mojo->host, $parsed_regex->{host},
    "Mojo and regex sub agree on 'host'" );
is( $parsed_mojo->port, $parsed_regex->{port},
    "Mojo and regex sub agree on 'port'" );
is( decode( 'UTF-8', url_unescape( $parsed_mojo->path ) ),
    $parsed_regex->{path}, "Mojo and regex sub agree on 'path'" );
is( decode( 'UTF-8', url_unescape( $parsed_mojo->query ) ),
    $parsed_regex->{query},
    "Mojo and regex sub agree on 'query'"
);
is( $parsed_mojo->fragment,
    $parsed_regex->{fragment},
    "Mojo and regex sub agree on 'fragment'"
); 
```

```
ok 1 - Mojo and regex sub agree on 'scheme'
ok 2 - Mojo and regex sub agree on 'userinfo'
ok 3 - Mojo and regex sub agree on 'host'
ok 4 - Mojo and regex sub agree on 'port'
ok 5 - Mojo and regex sub agree on 'path'
ok 6 - Mojo and regex sub agree on 'query'
ok 7 - Mojo and regex sub agree on 'fragment' 
```

看起来我们做得很好！您会注意到，由于我选择使用的 URL 包含非 ASCII 字符，并且`Mojo::URL`返回的字符串是经过 [URL 编码的](https://en.wikipedia.org/wiki/Percent-encoding)，所以我必须取消 URL 的转义，然后解码 unicode 字符串，以便将它们与我的代码返回的内容进行比较。此外，由于我将缺失的字段定义为空字符串，而不是将它们设置为`undef`，我不得不将`// ''`用于来自`Mojo::URL`的`userinfo`属性。

好的，所以我们看起来没有遇到任何错误，我们实际上同意了(这算哪里？)与已建立的模块。这样做肯定会有一些性能损失，对吗？让我们回到我们信赖的 [`Benchmark::Forking`](https://metacpan.org/pod/Benchmark::Forking) 模块:

```
 Rate               mojo from_scratch_regex
mojo                31204/s                 --               -72%
from_scratch_regex 110892/s               255%                 -- 
```

什么？我们比`Mojo::URL`做得好大约 255%？仔细想想，这可能是有道理的。我敢肯定，在那个模块内部还会发生更多的事情，以确保事情比我的代码所做的任何事情都要安全，而且它实现了一个具有字符串化等功能的整个类。

所以，它通过了我所有的测试，似乎与一个完善的模块一致，甚至在速度上击败了它。这可以用在你或我的下一个项目中，对吗？？

不会吧！首先，我没有进行详尽的测试。另外，我基本上是一个从网上随便找来的人，在几个小时内写了这篇文章。

不要试图为一个重要的应用程序编写自己的 URL 解析器，除非你真正理解“RFC 3986、RFC 3987 和支持 IDNA 和 IRIs 的统一资源定位器的 URL 生活标准”是什么意思。用类似`Mojo::URL`的东西代替。

好玩的副业？当然可以。

[此处](https://gist.github.com/yzhernand/7fccc513bfd06febf946d228c21f9b5d)见全解要诀。