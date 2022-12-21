# PHP 中的变异测试

> 原文：<https://dev.to/moxio/mutation-testing-in-php-45ph>

最近，我花了一个下午的时间在 PHP 中试验*突变测试*。在这篇文章中，我想分享背景，突变测试的主要思想，以及我从中学到的教训。

[![](img/c21691cbaec1bb0bd6e516251c5ca37f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Rwwfd6dX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AMM2MfxFwWD5TMibwWu2gBQ.jpeg)

### 莫西欧学院

在[莫西欧](https://www.moxio.com)时，我们会定期安排一次*莫西欧学院*会议。这意味着我们把正常的工作放在一边一个下午来学习新的东西或尝试最新的技术。你可以选择任何话题，只要它有利于你的个人发展，从而有利于你在公司的角色。只有几条规则:

*   你得决定一个话题，并提前宣布。这是为了提高团队内部的协同作用:也许有人已经对这个主题了解很多，并且有一些资源可以推荐。如果多人想学习同一件事，也许他们可以联合起来一起学习。
*   你必须为如何接近你的主题制定一个计划。这不需要非常广泛，只是一个粗略的草图就足够了。这个想法是，这迫使你集中你的学习努力，而不是只是阅读随机资源，没有任何总体方向的感觉。
*   你应该朝着某种可实现的目标努力。理想情况下，这是可以与团队其他成员共享的东西:工具、wiki 文章、一些概念验证、博客帖子或午餐学习演示文稿。同样，这有助于集中过程，有一些具体的最终结果，并在团队内传播获得的知识。

我们通常在下午结束时一起介绍我们的过程和结果，并(为那些想要的人)吃一个比萨饼。

### 突变检测

在最近的 Moxio 学院，我想在 PHP 中试验*突变测试*。我已经听说过变异测试和[感染](https://infection.github.io/)作为一种工具来做了几次，但从来没有抽出时间来亲自使用它。这似乎是一个好机会。

本质上，*突变测试*是一种测量测试套件质量的方法。一个工具生成你的测试源代码的许多副本(*突变体*)，但是以一种小的方式修改它们。这些小的修改基本上只是你作为程序员可能犯的错误，比如用一个`<=`替换一个`<`。一个高质量的测试套件将通过一个或多个失败的测试来检测这些修改(*杀死突变体*)。在一个次优的测试套件中，尽管修改了源代码，测试可能仍然是绿色的。在这种情况下，我们说的是一个*逃脱的突变体*。这些提供了一个机会，通过添加一个在给定修改下失败的测试来改进测试套件。*突变得分指标* (MSI，测试集检测到的突变体的百分比)提供了测试套件质量的衡量标准。

我的计划是对我们的一个内部库进行感染。我选择这个库是因为它的高(基于行的)代码覆盖率，这意味着高质量的测试套件。因此我想知道感染还能在其中找到什么“漏洞”。想法是将通过感染发现的逃逸突变转化为新的测试，致力于 PR 以增加项目的 MSI 作为主要可交付成果。这篇博文并不是最初计划的一部分，而是作为一种额外的方式来分享一路上学到的一些经验教训。

### 吸取教训

#### 突变检测牛逼！

我的主要收获是突变测试可以真正帮助你改进你的测试套件。即使在一个 96%行覆盖率的项目中，Infection 也发现了多个实际上没有被测试套件覆盖的场景。

下面是一个简化的例子。假设我们有一个函数来生成对墙上的啤酒瓶数的描述:

```
<?php
function describeBottles(int $amount = 99): string {
    return $amount . ' bottles of beer on the wall';
} 
```

我们可以像这样对这个函数进行测试:

```
<?php
class DescribeBottlesTest {
    public function testDescribesTheAmountOfBottlesOfBeer() {
        $this->assertSame('42 bottles of beer on the wall', describeBottles(42));
    }
} 
```

乍一看，这似乎是对函数的全面测试，实际上函数显示了代码覆盖报告中覆盖的所有行。然而，我们的测试并不检查参数的默认值。这意味着我们函数的一些行为，即默认情况下它描述了 99 个瓶子，没有被验证。当感染产生类似于
的突变时，它可以揭示这一点

```
12) /tmp/bottles.php:2    [M] DecrementInteger 
--- Original
+++ New @@ @@
<?php
- function describeBottles(int $amount = 99): string { + function describeBottles(int $amount = 98): string {
      return $amount . ' bottles of beer on the wall';
  } 
```

这里的`DecrementInteger` mutator 减少了源代码中出现的一个整数，如果我们按错了键盘上的键，这可能是我们自己造成的错误。这目前还没有被注意到，但是我们可以通过添加一个测试来解决这个问题，比如:

```
<?php
class DescribeBottlesTest {
    public function testDescribes99BottlesByDefault() {
        $this->assertSame('99 bottles of beer on the wall', describeBottles());
    }
} 
```

感染通常发现的其他未经测试的方面是异常消息和一些复杂逻辑中未被发现的路径。我为其中的大部分添加了测试或断言。对于逻辑来说，这也是(圈/N 路径)复杂度太高的一个标志。这些代码片段应该被重构，但是我把它安排在后面。

在第一次感染运行时，没有对测试套件进行任何更改，它产生了 89%的 MSI。这已经很好了，但是通过对测试套件进行一些添加，我设法将 MSI 提高到 93%。

#### 【误报】

然而，让 MSI 变得更高被证明是困难的。有时逃脱的变种人会改变代码中不重要的部分。我们认为，这些细节不构成相关行为，也不是该单位“合同”的一部分。那为什么它们会出现在代码里？嗯，有时它们必须是由于语法限制。以下面这段可能抛出异常的代码为例:

```
<?php
// ...
try {
    $database->executeSql("...");
} catch (DuplicateDatabaseKeyException $e) {
    throw new UserAlreadyExistsExeption("User $username already exists", 0, $e); 
} 
```

正如在以前的博客文章中所解释的，我们认为在正确的抽象层次抛出异常是很重要的:

[![arnoutboks image](img/fcc64a01da919b1fcd50e592be32dd45.png)](/arnoutboks) [## PHP 异常处理的最佳实践

### Arnout Boks ・ Jan 10 '19 ・ 9 min read

#php #exceptions #oop #softwaredesign](/moxio/best-practices-for-php-exception-handling-j5i)

这需要捕捉并重新抛出异常，如上面的代码片段所示。在那篇文章中，我还提到我们希望通过设置新异常的`$previous`-参数来保持与根本原因的联系。由于 PHP 的语法，这需要提供`$code`-参数，我们并不真正使用它。我们通常将它设置为`0`(默认值)，但老实说，我们一点也不关心它的值。

现在同一个`DecrementInteger`突变基因可能出现并产生这个突变:

```
53) /tmp/exception.php:289   [M] DecrementInteger 
--- Original
+++ New @@ @@
  } catch (DuplicateDatabaseKeyException $e) {
-     throw new UserAlreadyExistsExeption("User $username already exists", 0, $e); +     throw new UserAlreadyExistsExeption("User $username already exists", -1, $e); 
  } 
```

这种变异不会被我们的测试套件捕获(因为我们没有断言产生的异常的代码)，所以变异将会逃脱。在这种情况下，我们不在乎。我们不期望我们的测试套件检测到这一点，因为(对我们来说)变异后的代码和原始代码一样好。我们可以为异常代码添加断言，但这只是额外的工作，不会产生额外的价值。

我认为这是做突变测试时需要注意的事情。不是所有逃跑的变种人都一定是坏人。对于每一个问题，你必须问自己“如果我在代码中犯了这个‘错误’会不会不好？”。如果答案是否定的，就不要为逃跑的变异人费心了。

#### 加什么测试？

试图杀死一个变异人的主要困难之一是弄清楚应该增加什么样的测试。仅仅从代码中看到一个改变的行，并不总是清楚如何写一个在给定的行上会失败的测试。事实上，我工作的代码库是由一个同事写的，我还不完全了解它，这进一步放大了这一点。

我了解到 PHPUnit 生成的 HTML 代码覆盖报告在这里会有很大的帮助。如果你将鼠标悬停在一行被覆盖的代码上，它会显示哪些测试覆盖了那一行。通过这种方式，您可以查找哪些测试已经使用了变异的代码行。你想增加的杀死变异体的测试很可能是其中一个的变种。这将你的问题简化为分析这些“示例”测试，并推理当突变出现时，你可以改变它们以使其失败。

#### 它也改善了你的代码

不仅测试套件在我的突变测试实验中得到了一些更新；生产代码也得到了改进。有时感染产生的突变体实际上比原始版本更好！其中一个案例看起来有点像这样:

```
<?php
class SomeStore {
    public static function createWithInMemoryDatabase(): self {
        $database = $database ?? new SqliteDatabase(':memory:');
        // ...
    }
} 
```

在生成的变异中，有一个移除了`$database ??` null coalesce 操作符。这实际上是一个改进，因为 null coalesce 操作符在这里没有用！在函数的开始，`$database`总是空的，所以操作符总是解析到它的右边，创建一个新的数据库。这段代码是一个工件，当时这个方法被不同地命名，并允许通过一个`$database`参数注入一个定制的数据库。既然参数已经被移除，我们也可以去掉空的联合。虽然其他静态分析工具也可能发现这个死代码，但至少感染引起了我们的注意。

另一个突变体变得更好的例子是去除了一个`trim()`功能。在代码的那个地方，不可能有任何重要的或有问题的空白。因此，`trim()`-调用是不必要的，可以删除。

### 结论

一旦你有了一些突变测试的实践，它真的可以帮助你改进测试套件和代码。Infection 的设置和使用都很简单，这使得开始使用 PHP 变得相当简单。请记住，并不是所有逃脱的变种人都是问题，盲目追求 100% MSI 并不会增加价值。试试吧，让我知道你有什么体验！