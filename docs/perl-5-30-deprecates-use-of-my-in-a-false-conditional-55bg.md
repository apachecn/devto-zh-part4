# Perl 5.30 不赞成在假条件中使用 my()

> 原文：<https://dev.to/mitchjacksontech/perl-5-30-deprecates-use-of-my-in-a-false-conditional-55bg>

Perl 5.30.0 发布( [changelog](https://metacpan.org/pod/release/XSAWYERX/perl-5.30.0/pod/perldelta.pod) )。随之而来的是对我的重大改变。我已经很久没有面对 perl 的重大变化了。

[不赞成在假条件](https://metacpan.org/pod/distribution/perl/pod/perldiag.pod#Deprecated-use-of-my()-in-false-conditional.-This-will-be-a-fatal-error-in-Perl-5.30)中使用 my()。

我一直在自由地使用这个不推荐使用的语法，比如:

```
# This syntax generates a fatal error in perl 5.30
#
# Initialize $foo
#   If $bar tests true, $foo = 'foobar'
#   If $bar tests false, $foo = undef
my $foo = 'foobar' if $bar; 
```

Enter fullscreen mode Exit fullscreen mode

> Perl 中有一个长期存在的错误，当一个词法变量的声明包含一个假条件时，它会导致该变量在退出作用域时不被清除。

这可能会产生有趣的错误。以这种方式声明的作用域变量可能会意外地保存数据！

```
# Use this instead
my $foo;
if ( $bar ) {
  $foo = 'foobar';
}

# Or this
my $foo;
$foo = 'foobar' if $bar; 
```

Enter fullscreen mode Exit fullscreen mode

根据 [RT# 133543](https://rt.perl.org/Public/Bug/Display.html?id=133543#txn-1599917) 的说法，此语法不会总是触发弃用！因此，我觉得我不能相信在 5.30 下运行一个测试套件来识别所有违规的代码语句。

得知这种声明模式有问题时，还有人感到惊讶吗？