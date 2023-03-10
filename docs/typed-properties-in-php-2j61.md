# PHP 中的类型化属性

> 原文：<https://dev.to/brendt/typed-properties-in-php-2j61>

在 <abbr>PHP 7.4</abbr> 中添加了类型类属性，并对 <abbr>PHP</abbr> 的类型系统进行了重大改进。这些变化是完全选择加入和向后兼容的。

在本帖中，我们将深入探讨这一特性，但首先让我们总结最重要的几点:

*   类型化属性从 <abbr>PHP 7.4</abbr> 开始可用
*   它们只在类中可用，并且需要一个访问修饰符:`public`、`protected`或`private`；或者`var`
*   允许所有类型，除了`void`和`callable`

这是它们在行动中的样子:

```
class Foo
{
    public int $a;

    public ?string $b = 1;

    private Foo $prop;

    protected static string $static = 'default';
} 
```

如果你不确定类型的额外好处，我建议你先阅读[这篇文章](/blog/tests-and-types)。

继续阅读 https://stitcher . io/blog/typed-properties-in-PHP-74 # initialized 上关于类型化属性的详细信息