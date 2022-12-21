# PHP 元组

> 原文：<https://dev.to/blackcat_dev/php-tuples-4069>

我们如何称呼一只地鼠和一只大象约会？一个元组。

哇，这完全说不通...好了，我们现在应该继续前进了...

我已经在 golang 工作了一段时间，开始下意识地掌握一些模式。由于 golang 有[多个返回值](https://gobyexample.com/multiple-return-values "Golang - Multiple Return Values")，我发现这非常有用，并且是它的忠实粉丝，我开始越来越多地在 **php** 中这样做:

```
<?php

list($items, $count) = $contentRepository->getSlice($page, $perPage, $filters, $sorts);

// or the even sweeter shorthand syntax

[$items, $count] = $contentRepository->getSlice($page, $perPage, $filters, $sorts); 
```

我们在这里所做的只是析构普通的 ole '整数索引数组，在更多的 CS supply 圈子中通常称为一个[列表](https://en.wikipedia.org/wiki/List_(abstract_data_type) "Lists")。但是这种方法有一个缺点，数组有点像黑盒，我们不知道里面有什么，可能是任何东西。让我们看一下 ***getSlice*** 方法签名来更好地理解:

```
<?php

public function getSlice(int $page, int $perPage, array $filters = [], array $sorts = []): array;
{
    // something, something
    return [$items, 15];
} 
```

你看，我们不知道数组的成员是什么类型，也没有办法强制执行，所以如果我们不小心放了一些意外的值，我们很容易就会引入一些讨厌的错误。

我们可以通过在方法签名中添加 doc 块来稍微调整一下这种方法，这将有助于我们的 IDE 和静态分析工具，如 [PHPStan](https://github.com/phpstan/phpstan "PHPStan") 了解预期的值:

```
<?php

/**
 * @return array<int,string>;
 **/
public function getTitleAndName(): array;
{
    return ["Supreme Overlord", "Sasa Blagojevic"];
} 
```

等等，什么？为什么我们现在用一个不同的例子？嗯，这也有它的局限性， doc 块只有当数组的所有成员都是同一类型时才会增加值。在我们的第一个例子中，情况并非如此。那我们现在怎么办？

### 欢迎来到 **PHP 元组**

```
class Tuple implements ArrayAccess
{
    private $values;

    public function __construct(...$values)
    {
        $this->values = $values;
    }

    public final function offsetExists($offset)
    {
        return isset($this->value[$offset]);
    }

    public final function offsetGet($offset)
    {
        return isset($this->value[$offset]) ? $this->value[$offset] : null;
    }

    public final function offsetSet($offset, $value) {}

    public final function offsetUnset($offset) {}
} 
```

让我们仔细分析一下这段代码:

*   我们已经实现了 [*ArrayAccess*](https://www.php.net/manual/en/language.oop5.final.php "PHP ArrayAccess Interface") 接口，使得类 *Tuple* 的对象可以作为数组访问
*   我们已经将***offset***和***offset***方法留有空体，这样我们的元组是不可变的，我们不想因意外改变它们的状态而引入错误
*   我们已经将所有实现的方法 ***设为 final*** ，所以除了构造函数之外，其他开发人员无法覆盖它们并破坏我们元组的底层行为

这将是我们的通用元组。为了使事情更加严格和明确，我们将通过扩展我们的*通用元组*类并向构造函数添加类型来基于每个案例定制元组类:

```
<?php

class ContentSlice extends Tuple
{
    /**
     * ContentSlice constructor.
     * @param array<int, Content> $items
     * @param int $count
     */
    public function __construct(array $items, int $count)
    {
        parent::__construct($items, $count);
    }
} 
```

让我们把这个新发现的知识应用到我们的第一个例子中:

```
<?php

class ContentRepository 
{
    public function getSlice(int $page, int $perPage, array $filters = [], array $sorts = []): Tuple;
    {
        // something, something
        $items = [Content(), Content(), Content()];
        return new ContentSlice($items, 15);
    }
} 
```

现在，如果我们在 *ContentSlice* 构造函数中输入错误的值，代码就会爆炸。所以我们现在已经实现的是，我们既有多重返回值又有显式和严格的代码，就像在 golang 中一样。

[![Mind blown](img/34b8db6e6addfa98f0280ed9eb1ae688.png)](https://i.giphy.com/media/xT0xeJpnrWC4XWblEk/giphy.gif)

这就是为什么学习新语言是伟大的，你会伸展你的大脑，迫使它以一种不同的方式来解决问题，这是新语言的惯用方式。当你开始应用所有你潜意识或有意识学到的很酷的概念时，这些新知识就会在你的主要语言中产生收益。这是磨练技能的好方法。

我今晚有这个随机的想法，想和你们(我想象中的观众)分享。我觉得这很酷，我可能会在一个项目中尝试一下，看看它在现实世界的应用中表现如何，你对 **PHP 元组**有什么想法？