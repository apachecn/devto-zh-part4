# 把拉弗尔的收藏用到 Symfony

> 原文：<https://dev.to/juyn/using-laravel-s-collection-into-symfony-4nf8>

## 什么是收藏？

在 PHP 中，我们都习惯于使用数组。这对于简单的事情来说是很棒的，但是它会很快变得复杂！

我的意思是，谁从来没有努力使用内置方法来制作切片、数组映射甚至数组遍历呢？

就我个人而言，我总是要检查文档才能正确无误。

集合提供了另一种选择。Laravel 文档是这样描述的:

> Illuminate\Support\Collection 类为处理数据数组提供了一个流畅、方便的包装器

换句话说，它提供了一堆方法和助手来访问、转换和使用数据数组。例如，您有一个负责发送电子邮件的 API 端点。您在输入中获得了一个 Email 对象数组，但是在试图发送它们之前，您必须首先检查它们是否传递了一些断言。

```
 $emails->reject(function (Email $email) {
        return null === $email->subject;
    })->each(function (Email $email) {
        $this->send($email)
    }); 
```

很漂亮，对吧？是的，这是一个非常简单的代码，你可能会说它可以用 foreach 和 if 来完成，但是当你开始有严肃的业务逻辑和大量数据要过滤时，它就提供了可行性。

### Symfony 和收藏品

之前我讲的是 Laravel 对集合的实现。它有很多方法和助手，让你的生活更轻松。

但是，职业上我必须和 Symfony 一起工作。这很棒，不要误解我的意思，但是它的集合实现是非常基本的。

Doctrine 提供的 ArrayCollection(这是他的小名字)，允许你过滤、访问和映射数组数据，仅此而已。另一方面，Laravel 提供了更高级的方法，如“first”、“sum”或“toJson”。

### 怎样才能把 Laravel 的收藏放进 Symfony？

这应该很简单，对吧？因为，既然作曲家存在，我们所要做的就是

```
composer require laravel/collection 
```

对吗？

但是，嘿，生活并不总是那么容易！实际上，集合是 Laravel 框架的一部分，所以，如果你想要它，你必须提取它，把它复制到你自己的回购协议上，并观察 Laravel 的变化以保持最新。
不用了谢谢！

## 第一次尝试

一开始，我很自大。为什么不写自己的实现呢？

我是说，没什么复杂的。只是一堆方法...简单。
ExtendedArrayCollection 诞生了。名字说明了一切。我的项目基于 Doctrine 的 ArrayCollection，以最小化要做的工作，但也确保它在 Symfony 的任何地方都能工作。

有一段时间确实如此。直到它不再存在。要获得像拉弗尔那样好的东西，需要大量的时间，而我没有...

## 那么，为什么不用 Laravel 的集合呢？

回到谷歌，寻找一个比我更勇敢的人，也许有更多的时间。我发现了一个由 Matt Stauffer(紧缩公司)制作的包，它是 Laravel 系列的出口产品。

这太完美了。嗯，几乎完美。

Laravel 的实体和 Symfony 的有点不同。在 Laravel 中，属性被声明为公共的，而在 Symfony 中，它们通常是私有的或受保护的，有一堆 getters 和 setters。

可以想象，这是一个问题，因为在 Laravel 的集合中，对象数据是这样访问的:

```
 elseif (is_object($target) && isset($target->{$segment})) {
        target = $target->{$segment};
    } 
```

是的。这不能用于受保护的属性，因为您必须使用$taget->getSegment()来访问该值。

因为我真的讨厌数组集合，所以我决定不放弃。因此，我从 zincco/collect 包中创建了一个 fork 来做一些调整，使我可以将 Laravel 的收藏应用到 Symfony 中。

### 代码修改

好吧，那么我需要做的就是找到一种访问数据的方法，不管它们是否可见。

我有几个选择:

*   断言“getSegment”方法存在，如果不存在，则尝试用->segment 访问值。
*   总是假设因为我在 Symfony 中，getter 将总是被设置
*   使用 ReflectionObject

我选择使用第三个选项，使用 ReflectionObject，因为它提供了一种更安全的方法。
代码变成了:

```
 elseif (is_object($target)) {
        $reflectedObject = new \ReflectionObject($target);

        // Use Reflection class to check if property even exist, otherwhise let's use default value

        if (!$reflectedObject->hasProperty($segment)) {
            return value($default);
        }

        $p = $reflectedObject->getProperty($segment);
        // If property is public, no need to get all the way down
        if ($p->isPublic() && isset($target->{$segment})) {

            $target = $target->{$segment};
        } else {
            // Make the property accessible before geting its value
            $p->setAccessible(true);
            $target = $p->getValue($target);
    } 
```

就这样了，伙计们。我只需要在自己的命名空间上做一个集合类，extend risinco version，我的 Symfony 就可以使用令人惊叹的 Laravel 的集合了！

### 接下来是什么？

这已经很酷很方便了，但是我想要更多。
接下来的步骤:

*   重写原则，使其使用集合而不是 ArrayCollection
*   创建一个包含文档和示例的适当的存储库
*   找到简化名称空间管理的方法
*   进行单元测试！

如果你们中有人对这个包感兴趣:

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [聚音](https://github.com/Juyn) / [收藏](https://github.com/Juyn/collect)

<article class="markdown-body entry-content container-lg" itemprop="text">

# 收集

在 Laravel 的超赞系列中加入对 Symfony 的支持

</article>

[View on GitHub](https://github.com/Juyn/collect)