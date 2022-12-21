# 良好实践:在 PHP 中处理错误和异常

> 原文：<https://dev.to/anastasionico/good-practices-handling-error-and-exceptions-in-php-5d8c>

## 关于本系列:

在这个系列中，我们将探索 web 开发者在创建或管理 PHP 代码时必须注意的最佳实践。
[杀毒、验证和转义](http://anastasionico.uk/blog/good-practices-how-to-sanitize-validate-and-escape-in-php)
[安全和管理密码](http://anastasionico.uk/blog/good-practices-php-security-manage-password)
[处理错误和异常](http://anastasionico.uk/blog/good-practices-handling-error-and-exceptions-in-php)

## 简介

*1879 年 10 月 21 日 19:15:13*

*新泽西门洛帕克*

一股劲风使夜晚变得凉爽，

最近天气一直不太好，在过去的两周里雨一直在下，而且似乎不会很快停止。在一家工厂里，几个人正在拼命工作，他们认为这有可能改变世界。

[![](img/02e4f0f1a027c3507cb04a7f071f6bf7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_1cGL3zW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://anastasionico.uk/img/1/Edison_-_his_life_and_inventions_%281910%29_%2814778250434%29.jpg)

最后的测试将在几分钟内发生，它将是数周研究、努力工作和计算的结果。

3

2

1

**天王！！**

手电筒照了一下房间，一秒钟后又暗了下来，又一天过去了，又一次失败，又一次失误。

那就是现在的第 9999 个。

> “我没有失败。我刚刚发现了一万种行不通的方法。”
> ***爱迪生***

如果你听过像布莱恩·特雷西、托尼·罗宾、金克拉或吉米·罗恩这样的励志演说家，你就会知道他们都对世界各地的学校管理方式有意见。

在大学和学院里，错误受到惩罚而不是欢迎，

**然而在现实世界里，错误是你变得更好的唯一途径。**

在 web 开发中尤其如此。尝试和错误绝对是变得伟大和提高技能的最佳方式。

同样，我们需要小心谨慎，注意自己犯的错误。

犯错误的原因是我们可以管理并从中吸取教训。PHP 使得处理、管理和学习代码中可能出现错误变得容易。

## 如何管理 PHP 7 中的错误

如果你问我 PHP 5.6 和 PHP 7 或 PHP 7.1 之间改进最大的特性是什么，我肯定会把错误处理放在前三位。我们钟爱的编程语言的第七个版本给我们带来了一个有明确目标的界面:

它需要容易实现并且在很多情况下有用。

**throwable 接口，**

**实际上，这两种类型的类(错误类和异常)和 supply 以及一些我们可以用来更好地理解和分析我们的错误的方法都可以实现。**

在这里你可以看到这个类以及如何使用它:

```
Throwable {
    abstract public getMessage ( void ) : string
    abstract public getCode ( void ) : int
    abstract public getFile ( void ) : string
    abstract public getLine ( void ) : int
    abstract public getTrace ( void ) : array
    abstract public getTraceAsString ( void ) : string
    abstract public getPrevious ( void ) : Throwable
    abstract public __toString ( void ) : string
}
Try {
    // some code
} catch (Throwable $e) {
    echo "an instance of class Throwable has been caught";
} 
```

上面的代码片段展示了你可以用来调试脚本中的 PHP 错误的所有方法(有很多类型提示)

以及你可以用来尝试代码块并抛出错误或异常的实际代码。

## 错误，错误无处不在！

任何在 PHP 5.6 之前处理过错误的人都知道它是什么类型的痛苦，以及在调试阶段他需要处理的头痛问题。

以前错误是在引擎内部产生的，只要不是致命错误，你都可以处理。

你还需要考虑到错误和异常在你的代码中是两件完全不同的事情，这给已经很复杂的情况又增加了一层难度。

从 PHP 7 开始，致命错误导致抛出错误异常，而**你可以用定制的方法轻松管理非致命错误**

比如，我可以运行一些代码，只在致命错误发生时抛出错误。
T12】

```
try {
    echo $thisVariableIsNotSet;
    inexistentFunctionInvoked();
} catch (Error $e) {
    echo "Error: $e->getMessage()";
}
// Error: Call to undefined function inexistentFunctionInvoked(); 
```

[如果这是你第一次处理错误，或者你看到一个尝试捕捉，这可能看起来令人困惑，你可能会觉得你想后退一点，](http://anastasionico.uk/blog/php-basics)

[不要担心，在 Php 基础知识中你会找到你需要知道的一切，为阅读这篇文章做好准备。](http://anastasionico.uk/blog/php-basics)

请耐心听我解释发生了什么:

在我们的伪代码中，我们必须元素化一个变量和一个函数，**它们都没有被定义**，因此是不存在的。

**PHP 处理未设置变量与处理未定义函数不同，**

第一个只是无效变量注意错误，web 开发人员工作时间长，这种情况经常发生，

第二个错误是一个实际的函数，其中可能有一些重要的逻辑。

这就是为什么在 PHP 中一个未定义的函数是一个严重的问题，一个致命的错误。

try 块中的这两行代码允许 catch 实例化 Error 类的一个实例(变量 *$e* )。

*$e* 实现了 *Throwable* 接口，这意味着它可以使用你在上一段看到的所有方法。因此*$ e->getMessage()*；

## 创建定制的错误处理程序

可能会出现一些不属于错误类别的错误。

**如果这些错误不是致命的，PHP 允许你，作为一个开发者，定义定制的函数并以你喜欢的方式处理它们。**

为此，您需要使用 *set_error_handle()* 函数。

这个函数接受一个带有你想要使用的函数名的字符串，或者一个包含一个对象和你正在调用的方法名的数组。

该函数可以停止脚本，

如果你不想让它继续运行或者返回值并在代码首先调用该函数时继续运行。

让我们来看看下面一个实用而简单的例子。

*set_error_handle()* 不能管理致命错误，这意味着，为了测试它，我们需要模拟一个警告(被零除也可以)，

然后我们将定义函数的内容，并在错误发生时捕捉它。

```
function myErrorHandler($errno, $errstr, $errfile, $errline) {
    echo "An error occurred in line {$errline} of file {$errfile} with message {$errstr}";
}
​
set_error_handler("myErrorHandler");
​
try {
    5 / 0;
} catch (Throwable $e) {
    echo $e->getMessage();
}
​
// An error occurred in line 1 of file /index with message Division by zero 
```

不要忘记，您希望在生产环境中隐藏错误，当错误发生时，它必须只在本地或临时服务器上可见。

事实上，在产品中显示一个错误会给黑客或恶意用户一个很大的提示，说明您的网站存在漏洞。

为此，你可以在 php.ini 中编辑 3 个设置

*   **display_errors** 如果设置为 false，将抑制错误；
*   **log_errors** 将错误存储到日志文件中；
*   **error_reporting** 配置哪种类型的错误触发报告；

## 错误处理功能

PHP 有几个函数可以使处理错误和任务变得简单。在这一节中，你会找到对每个函数的简要描述。

*   **debug _ back trace()**它接受一些参数，如 options 的标志和一个限制结果的数字，并生成一个脚本如何到达它所在位置的摘要，它返回一个数组变量；
*   debug _ print _ back trace()它的工作方式与前一个类似，但它不是创建一个跟踪数组，而是以倒序的时间顺序打印它们；
*   **error _ get _ last()**它返回一个关联数组，包含脚本中发生的最后一个错误的信息；
*   **error _ clear _ last()**重置 PHP 的内部错误日志，如果在 error_get_last()之前使用后者将返回 null
*   **error _ log()**它需要一个强制消息作为参数，并将其发送给定义的错误处理例程；
*   **error _ reporting()**这个函数要么不需要参数，要么需要错误常数作为参数(你可以在这里找到完整的列表【https://www.php.net/manual/en/errorfunc.constants.php)并设置 PHP 应用程序在处理错误时必须具有的精确度；
*   **set _ error _ handler()**和**set _ exception _ handler()**设置处理错误和异常的定制函数；
*   **restore _ error _ handler()**和**restore _ exception _ handler()**在 set _ error _ handler()和 set _ exception _ handler()之后使用，目的是将处理程序恢复到之前的错误处理程序，可以是内置函数，也可以是用户自定义函数；
*   **trigger _ error()**该函数通过接受一条消息作为强制参数和一个 error_type 的标志作为可选参数来触发一个错误；
*   **用户 _ 错误()****的别名触发器 _ 错误()；**

[PHP 手册提供了预定义常数的完整列表，你可以在上面的](https://www.php.net/manual/en/errorfunc.constants.php)
的一些函数中用作标志

## 有哪些例外

异常是 PHP 相对较新的特性，它们只在 PHP 5 中实现，但它们很快成为任何面向对象编程脚本的核心部分。

**异常是脚本的状态，需要特殊处理，因为脚本没有按预期运行。**

错误和异常都只是实现了 *Throwable* 接口的类。

像 OOP 世界中的任何其他类一样，可以扩展，

它允许创建错误层次结构和定制处理异常的方式。

需要注意的是，不能声明自己的类，然后决定抛出异常，这可能会导致错误和误解。

唯一能抛出错误的类是那些实现了可抛出类的类。

我们来玩个游戏，看看代码，回答下面的问题。

```
class MainException extends Exception {}
class SubException extends MainException {}
​
try {
    throw new SubException("SubException thrown");
} catch (MainException $e) {
    echo "MainException thrown" . $e->getMessage();
} catch (SubException $e) {
    echo "SubException thrown" . $e->getMessage();
} catch (Exception $e) {
    echo "Exception thrown" . $e->getMessage();
} 
```

哪个异常被抛出？

在下面的例子中，抛出的异常是*子异常*，它继承自*主异常*，后者扩展了*异常*。

**从第一个(最上面的那个)到最后一个对程序块进行评估，当异常匹配给定的类名时，它被触发。**

以上可以被认为是最佳实践，因为我们缩小了我们想要展示的信息的范围。如果我们的类都不匹配，我们使用 PHP 类异常进行全面检查。

### 一次捕捉多个异常

到目前为止，我们已经看到了几次尝试和几次捕捉。

每个代码块用不同的错误回显一个消息，

想想在我们创建十几个不同异常的情况下，

我们应该增加一打滑车吗？如果我们想以同样的方式处理几种类型的错误呢？

**为了管理这种情况 PHP 提供了管道关键字“|”；**

下面举例说明如何使用:

```
class MainException extends Exception {}
class SubException extends Exception {}
​
try {
    throw new SubException;
} catch (MainException |  SubException $e) {
    echo "Exception thrown: " . get_class($e);
}
// Exception thrown: SubException 
```

## 最后关键字

我相信你在过去已经见过 switch-case 条件结构，

[如果没有，我有一整篇关于 PHP 条件语句的文章](http://http://anastasionico.uk/blog/php-if-switch)。

**switch 语句的元素之一是，如果最终没有一个情况被评估，那么一个“默认”程序块(如果存在的话)将会运行。**

【try a catch】通过提供关键字 finally

允许做一些类似于你的代码的事情

```
try {
    $handle = fopen("c:\\folder\\resource.txt", "r");   
    // do some stuff with the file
​
    throw new Exception("Exception thrown");
} catch (Exception $e) {
    echo "Exception thrown" . $e->getMessage();
} finally {
    fclose ( resource $handle );
} 
```

如上面的例子所示，当我们需要关闭之前在 try 块中打开的文件时，通常会用到这个关键字。

你必须记住，finally 块中的代码总是被执行的，即使在脚本的前面抛出了异常。

但是只要你认为这对你的案子有帮助，你就可以随意使用它。

* * *

如果你认为这篇文章是有用的，并且你想了解更多关于 PHP 的良好实践，点击下面的图片

[![http___eepurl.com_dIZqjf.jpg](img/e104041aea8289e9069a8d42a86a4c41.png)](http://eepurl.com/dIZqjf)

* * *

## 结论

托马斯·爱迪生出身卑微，历史资料证明他不是最聪明的人。总之，他能够让自己周围都是富有创造力和热情的人，加上能够深刻分析自己的错误并从中吸取教训，这使他成为有史以来最优秀的发明家之一，当然也是他那个时代最富有和最著名的人之一。

**我可以毫不犹豫地说，我们作为 web 开发人员也可以这样做。也许我们永远不会发明出世界著名的应用程序，或者用我们的网站改变数十亿人的生活，但毫无疑问，通过从错误中学习，我们将更快地提高我们的技能。管理错误是其中的一个基本部分。学习如何正确地做到这一点，并掌握 PHP 及其组件(如 PHPUnit 和 Sentry)中所有可用的可能性将使这项任务对我们每个人来说都很容易。**

你现在可以用来管理你的错误的有用组件或网站:
filp/oupes
nette/Tracy
vimeo/psal
sebastianbergmann/phpunit
code ception/code ception
getsentry/sentry
[https://phpcodechecker.com](https://phpcodechecker.com)
T26】https://www.piliapp.com/php-syntax-check