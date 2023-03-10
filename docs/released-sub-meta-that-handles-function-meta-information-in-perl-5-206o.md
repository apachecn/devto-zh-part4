# 在 Perl 5 中处理函数元信息的 Released Sub::Meta

> 原文：<https://dev.to/kfly8/released-sub-meta-that-handles-function-meta-information-in-perl-5-206o>

[Sub::Meta -处理子程序元信息-metacpan.org](https://metacpan.org/pod/Sub::Meta)

`Sub::Meta`是处理元信息的模块，例如子程序名称和子程序输入/输出。
还有类似的`Sub::Identify`、`Sub::Util`、`Sub::Info`等模块，但是写`Sub::Meta`是因为它不能处理子程序输入/输出的元信息。

基本用法如下。
您可以根据编码参考得到元信息:

```
use Sub::Meta;

sub hello($) :mehtod { }
my $meta = Sub::Meta->new(sub => \&hello);
$meta->subname;     # hello
$meta->sub;         # \&hello
$meta->subname;     # hello
$meta->fullname;    # main::hello
$meta->stashname;   # main
$meta->file;        # path/to/file.pl
$meta->line;        # 5
$meta->is_constant; # !!0
$meta->prototype;   # $
$meta->attribute;   # ['method'] 
```

您还可以处理元信息，而无需传递代码引用:

```
my $meta = Sub::Meta->new(subname => 'foo');
$meta->subname; # foo 
```

可以添加功能输入/输出元信息:

```
# INPUT
my $parameters = Sub::Meta::Parameters->new(
  args => [
    { name => '$a', type => 'Int' },
    { name => '$b', type => 'Int'},
  ],
  nshift => 1,
);

$meta->set_parameters($paramters);
$meta->parameters->args;
# [
# Sub::Meta::Param->new({ name => '$a', type => 'Int' }),
# Sub::Meta::Param->new({ name => '$b', type => 'Int' })
# ]

# OUTPUT
my $returns = Sub::Meta::Returns->new(
  scalar => 'Int',
  list   => 'Int',
);
$meta->set_returns($returns);
$meta->returns->scalar; # 'Int' 
```

让我们深入了解使用函数输入和输出元信息的动机。

Perl5 有很多验证函数输入的模块，比如`Params::Validate`、`Data::Validator`、`Smart::Args`、`Function::Parameters`、`Type::Params`等。

每个模块都有不同的可用性，我觉得有点紧张。另外，我想做静态分析，因为我在注释类型。

因此，我认为如果有一个对象来处理函数输入的元信息，那么在一个统一的 I/F 中会更容易处理。

函数输出也是如此。

此外，当试图在 Perl5 中表达类似 Java 的接口(如`Function::Interface`)时，如果函数的输入/输出易于处理，这将非常有用。

当前的`F::I`在`Function::Parameters`和`Function::Return`的实现中是粘性的，我认为它不能处理许多应用程序并且缺乏灵活性。

这就是为什么我创建了能够处理包括函数输入和输出在内的元信息的`Sub::Meta`。如果好的话我很乐意尝试！仅此而已！