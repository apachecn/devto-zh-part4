# 我从未写过的 Perl:代码片段

> 原文：<https://dev.to/jplindstrom/perl-i-never-write-snippets-3ae0>

Emacs 有这个很酷的包`yasnippet`，是一个简单的模板扩展系统。

例如，当我声明一个方法时，我可以输入一个简单的触发字:`argsm`(方法的参数)，点击`<Tab>`，触发字扩展到相应的模板中(|是光标(或者 Emacs 术语中的“点”):

```
 my $self = shift;
    my (|) = @_; 
```

这显然是需要做的事情。的。时间。

在这种情况下，我也在没有方法参数时使用它，因为事实证明键入`argsm<Tab>`并删除当前行比键入`my $self = shift;`要简单得多。

片段是一种非常有用的机制，可以减少做普通的、稍微复杂的事情时的摩擦(T2)。减少摩擦很重要，因为这能让你专注于你真正想要完成的事情，而不是死记硬背的语法(以及像`()$@_`这样的变化无常的字符)。

是 Emacs 的一个很好的 snippets 包；我相信你的编辑器有类似的功能/插件。

# 亚尼普茨

下面是实际的代码片段文件`snippets/perl-mode/argsm` :

```
#name : methods args -- my $self = shift; my () = @_;
# --
my \$self = shift;
my ($0) = @_; 
```

作为代码片段扩展的一部分，你可以点击`<tab>`在特定的位置之间移动(制表位，像`$1`、`$2`等。)中，通常在那里键入自定义文本。`$0`是扩展过程结束的最后一个制表位，您可以继续编辑。

制表位文本也可以有一个默认值，它可以是一个字符串，或者来自调用函数，或者从您在代码段中的其他地方键入的内容中映射出来。

这是非常强大的，特别是如果您添加一些自己的简单的 elisp 函数。我们将在下面看到其中的一些东西。

还有很多*比`yasnippet`更*，比如不同的触发机制、下拉菜单、完成支持等等。从小处着手，从简单做起。

你可以在本文末尾找到更多的`yasnippet`信息和安装说明。

# Perl 代码片段

附带了许多现有的各种语言和模式的小代码片段。

然而，我并不使用大多数固定的 Perl 代码片段:它们大多是常规的 Perl 关键字结构，一开始就不需要输入。相反，以下是我自己定义的一些，按照实际使用和效用的顺序排列:

## 自变量拆包

我在键入了方法框架(括号用`autopair-mode`)之后就使用了这些方法:

```
sub basket_total_price {
    |
} 
```

### argsm

```
#name : methods args -- my $self = shift; my () = @_;
# --
my \$self = shift;
my ($0) = @_; 
```

### args

```
#name : args my () = @_;
# --
my ($0) = @_; 
```

但是，在代码片段中包含整个子框架、花括号等等不是更方便吗？

当然，你可以这么做。但我发现，只有在摩擦开始增加的地方，尝试将自动化部件连接到流中，而不去管低摩擦部分，这是值得的。所以你跟着输入，当它变得令人讨厌时，你切换到插入一个片段。

此外，在编辑已经存在的 sub 时，可以添加较短的代码片段。这使得更小、更有针对性的文本在更多情况下有用。

## 方法/子 POD

为*方法签名*(即子名称、参数和返回值)编写 POD 是一个非常好的习惯:花更多的时间思考正在发生的事情通常是一件好事。

在某些方面，这样做就像是 TDD 的中途之家(好吧，也许是第五路之家)，因为写下来会让你思考这个方法做什么，它需要什么参数，它返回什么，任何可能适用的特殊情况，以及事情会如何出错。基本上是想象应该和能够如何调用它，而不是一直写代码来实现。

### podx

在 sub 声明的上面调用它，展开成样板 POD 文本:

```
#name : pod above sub -- =head2 name() :
# --
=head2 ${1:`(my-yas-perl-next-sub-name)`}($2) :$3

$0

=cut 
```

这个片段很好地演示了不同的制表符`$1`、`$2`、`$3`和最后的`$0`。第一站是预先填入的子程序名，当我点击`<tab>`时，指针跳转到下一个我可以继续输入参数的地方，等等。

子名称的默认值是事情变得有趣的地方。子名称来自调用 elisp 函数`my-yas-perl-next-sub-name`，在本例中是一个简单的正则表达式匹配:

```
(defun my-yas-perl-next-sub-name ()
  (interactive)
  (save-excursion
    (if (search-forward-regexp "sub +\\([a-z0-9_]+\\)" nil t)
        (match-string 1)
      ""))) 
```

所以有了这个:

```
podx|
sub get_workflow_type_row { 
```

变成了这样:

```
=head2 |get_workflow_type_row() :

=cut

sub get_workflow_type_row { 
```

最后，在记录了方法之后:

```
=head2 get_workflow_type_row($workflow_type) : $workflow_type_row | undef

Return a $workflow_type_row for the $workflow_type
(e.g. "multi_role"), or undef if it doesn't exist (either because
it's invalid or because the company has no Workflows with that
$workflow_type).

=cut 
```

## Moo/Moose 懒惰属性

这些是基本的 Moo/Moose 属性，带有一个惰性生成器，很容易成为我们代码中最常见的属性样式。如果不是，插入的片段文本很容易调整。

为此使用触发词`haslm` (Moo)和`hasls` (Moose)也有获得正确语法的好处，因为这是恼人的不同，我永远也不会记得哪个是哪个。

### haslm

```
#name : has => () Moo attribute with lazy builder
# --
has ${1:name} => ( is => "lazy" );
sub _build_$1 {
    my $self = shift;
    $0
} 
```

### hasls

```
#name : has => () Moose attribute with lazy builder
# --
has ${1:name} => ( is => "ro", lazy_build => 1 );
sub _build_$1 {
    my $self = shift;
    $0
} 
```

如果你看看写着`${1:name}`的部分，你会看到另一个很酷的`yasnippet`功能:*镜像*。当我们键入属性名称时，构建器子名称会实时更新:

```
has csv_writer| => ( is => "lazy" );
sub _build_csv_writer {
    my $self = shift;

} 
```

## 其他 Moo/Moose 属性

为了完整起见，这里还有一些其他不常用的属性声明。

### 个音节

```
#name : has => () Moose/Moo attribute with required => 1
# --
has ${1:name} => ( is => "ro", required => 1 ); 
```

### 有

```
#name : has => () Simple Moose/Moo attribute
# --
has ${1:name} => ( is => "ro" ); 
```

### hasbm

```
#name : has => () Moo attribute with lazy "builder" subref
# --
has ${1:name} => ( is => "lazy", builder => sub { $0 } ); 
```

## 豆荚碎片

不常用，但还是有点用:POD `=head1` sections。

### podn

```
#name : name with current package -- =head1 NAME
# --
=head1 NAME

${1:`(ps/current-package-name)`} -- $0

=head1 DESCRIPTION

=cut 
```

这个例子很有趣，因为它重用了来自[Devel::perly sense-Copy Package Name](https://metacpan.org/pod/Devel::PerlySense#Edit-Copy-Package-Name)的 elisp 函数来获取带有当前包名的字符串，这就是我们想要的`=head1 NAME` POD 部分。

大家不要再敲已经知道的东西了！

### 颇达

```
#name : pod =head1 ATTRIBUTES
# --
=head1 ATTRIBUTES
$0
=cut 
```

### 可以

```
#name : pod =head1 METHODS
# --
=head1 METHODS
$0
=cut 
```

### podcm

```
#name : pod =head1 CLASS METHODS
# --
=head1 CLASS METHODS
$0
=cut 
```

### 豆荚

```
#name : pod =head1 SUBROUTINES
# --
=head1 SUBROUTINES
$0
=cut 
```

# 开始使用`yasnippet`

点击此处阅读更多关于`yasnippet`的信息:

*   [精彩介绍](https://cupfullofcode.com/blog/2013/02/26/snippet-expansion-with-yasnippet/index.html)
*   [文档](http://joaotavora.github.io/yasnippet/)
*   [GitHub 回购](https://github.com/joaotavora/yasnippet)
*   [EmacsWiki](https://www.emacswiki.org/emacs/Yasnippet)

从 [ELPA](https://elpa.gnu.org/packages/yasnippet.html) 开始安装。

# __END__

就是这样！

读完这篇文章后，我相信你对自己的代码片段已经有了一些很好的想法，或者你已经有了一个有用的代码片段库？

请在评论中分享！<small>(或在[reddit.com/r/perl](https://www.reddit.com/r/perl/comments/c045pj/perl_i_never_write_snippets/))</small>