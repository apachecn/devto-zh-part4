# 一个更简单的 WordPress 单元测试设置

> 原文：<https://dev.to/alexmacarthur/a-simpler-unit-testing-setup-for-wordpress-1ki9>

当我刚开始从事 web 开发时，我记得要理解像单元测试这样的事情的原因、内容和方法，在概念上是多么的困难。更困难的是，WordPress 是我花了大部分时间的环境——这个平台并不以其强大的单元测试文化而闻名。

据我所知，WordPress 中单元测试最推荐的资源是 WP-CLI 提供的[脚手架。运行一个命令，它会做一些事情，比如下载一份 WordPress，建立一个测试数据库，并提供一些将你的测试与 WP 基础设施本身集成的好方法。问题是每次我尝试设置主题和插件时，都会遇到意想不到的数据库或文件结构问题。我花了相当多的时间研究它们，然后*也许*有足够的精神动力去写一个测试。](https://make.wordpress.org/cli/handbook/plugin-unit-tests/)

肯定有更简单的方法来设置这些东西。T3】

我喜欢 WP-CLI 方法所提供的东西。由`WP_UnitTestCase`(你可以扩展的类，而不是`PHPUnit\Framework\TestCase`)单独提供的帮助器方法可能会使解决设置冲突变得值得。但有时，你只是想开始写一些该死的测试来为你的主题或插件建立一些价值。在这种情况下，有一个更好的方法:**自己动手。**没有听起来那么恐怖。

## 让我们开始准备吧

在这次演练中，我将使用我的 [wp-skateboard](https://github.com/alexmacarthur/wp-skateboard) 设置，它在 Docker 上运行，但不管你是使用这个，流浪者，还是其他什么都没关系。但是如果您使用 Docker，请确保在您的运行容器中运行以下任何命令。为了帮助实现这一点，`wp-skateboard`允许您运行`make bash`来轻松地使用 bash 提示符进入容器。

我们还将使用 Composer 进行包管理和自动加载。因为我发现大多数教程都集中在插件上，所以我们也将坚持下去。

#### 用 Composer 安装 PHPUnit。

首先，`cd`进入您的插件，并按照`composer init`命令的提示进行操作。如果目录中已经有了一个`composer.json`文件，那么继续前进。

之后，添加一些自动加载的数据，这些数据以后会派上用场。在这里，我们告诉 Composer 在将某些文件命名为`PluginTests`时，自动将它们包含在`tests/`目录(我们的测试将驻留在这里)中。一会儿再详细讲讲。

```
"autoload-dev": {
    "psr-4": {
        "PluginTests\\": "tests/"
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们需要安装 PHPUnit。

`composer require phpunit/phpunit --dev`

完成后，通过运行以下命令来测试您的安装。

`vendor/bin/phpunit`

您应该会看到一堆类似这样的信息:

```
PHPUnit 8.2.3 by Sebastian Bergmann and contributors.

Usage: phpunit [options] UnitTest [UnitTest.php]
        phpunit [options] <directory>

Code Coverage Options:

    --coverage-clover <file> Generate code coverage report in Clover XML format
    --coverage-crap4j <file> Generate code coverage report in Crap4J XML format
    ... 
```

Enter fullscreen mode Exit fullscreen mode

#### 通过我们的 phpunit.xml 文件加载 WordPress。

由于这是一个 *WordPress* 插件，为了有效地测试我们的代码，我们可能需要访问一些 WP 核心功能。为了使所有这些都可用，我们将在测试期间通过在我们的`phpunit.xml`文件中引导来加载 WP 核心。将一些基本配置放入该文件，并特别注意`bootstrap`属性:

```
<?xml version="1.0" encoding="UTF-8"?>
<phpunit bootstrap="bootstrap.php">
    <testsuites>
        <testsuite name="WP Unit Testing">
            <directory suffix="Test.php">./tests/</directory>
        </testsuite>
    </testsuites>
</phpunit> 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们正在加载一个`bootstrap.php`文件，它将在我们的插件目录中创建，加载两个东西:

*   `autoload.php` -这是通过运行`composer install`生成的，将负责我们之前添加的 PHP 自动加载配置。
*   这是一个来自 WordPress core 的文件，它将加载 CMS 功能，当我们测试插件时，我们需要运行插件的代码。

```
<?php 

// bootstrap.php

require_once( __DIR__. "/../vendor/autoload.php");
require_once( __DIR__. "/../../../../wp-load.php"); 
```

Enter fullscreen mode Exit fullscreen mode

有了这些，这两个文件将在我们的测试套件每次运行时被加载，为我们提供测试所需的资源。

另外，请注意我们的`phpunit.xml`文件中的这一行:

```
<directory suffix="Test.php">./tests/</directory> 
```

Enter fullscreen mode Exit fullscreen mode

这样做的目的是指定我们在`./tests`目录中作为测试运行的命名文件的类型。因为后缀设置为“Test.php”，所以任何以该字符串结尾的文件都将运行。

此时，尝试再次运行`vendor/bin/phpunit`。这一次，您应该会看到更像这样的内容:

```
PHPUnit 7.3.5 by Sebastian Bergmann and contributors.

Time: 1.03 seconds, Memory: 22.00MB

No tests executed! 
```

Enter fullscreen mode Exit fullscreen mode

## 测试时间

在插件中创建一个`tests`目录，并在其中创建一个`SomeClassTest.php`文件。记住，文件名很重要。现在，让我们来验证一下，我们可以顺利地运行测试。扔些无意义的测试进去。当一个方法的名字以`test`开头时，它就被认为是一个测试。

```
<?php

class SomeClassTest extends \PHPUnit\Framework\TestCase
{
    public function test_shouldWork()
    {
        $this->assertTrue(true);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

运行`vendor/bin/phpunit`应该会产生如下结果:

```
PHPUnit 8.2.3 by Sebastian Bergmann and contributors.

. 1 / 1 (100%)

Time: 2.59 seconds, Memory: 54.25 MB 
```

Enter fullscreen mode Exit fullscreen mode

噪音。

#### 让我们把事情变得更 WordPress-y。

从现在开始，我们可以根据需要开始利用 WordPress 生态系统。举一个愚蠢的例子，假设我们有一个方法，可以根据文章的标签，以情绪化的方式检索文章的内容。具体来说，如果帖子被标记为`angry`、`excited`或`obnoxious`，我们将对内容进行不同的格式化。

```
<?php

/*
Plugin Name: Unit Testing Plugin
*/

function getEmotionalizedContent($postId)
{
    $content = get_the_content(null, false, $postId);

    if (has_tag('angry', $postId)) {
        $content = strtoupper($content);
    }

    if (has_tag('excited', $postId)) {
        $loudContent = array_map(function ($character) {
            if ($character === ".") {
                return str_repeat("!", rand(1, 10));
            }
            return $character;
        }, str_split($content));
        $content = implode($loudContent);
    }

    if (has_tag('obnoxious', $postId)) {
        $content = str_replace(" ", " 👏 ", $content);
    }

    return $content;
} 
```

Enter fullscreen mode Exit fullscreen mode

因为我们的 PHPUnit 配置设置为导入 WordPress 核心，我们的测试将访问这个插件(确保它在 WP 管理中是活动的！)，以及 WordPress' `wp-load.php`文件加载的其他所有东西。所以，我们应该准备好投入&测试。

首先，让我们给`SomePostClass`添加一些代码，这将为我们提供一个在完成后可以操作和清理的帖子。

```
class SomePostClass extends \PHPUnit\Framework\TestCase
{
    public $testPostId;

    protected function setUp(): void
    {
        $this->testPostId = wp_insert_post([
            'post_title' => 'Sample Post',
            'post_content' => 'This is just some sample post content.'
        ]);
    }
    protected function tearDown(): void
    {
        wp_delete_post($this->testPostId, true);
    }

    //... tests will go here.
} 
```

Enter fullscreen mode Exit fullscreen mode

利用`setUp()`和`tearDown()`，我们正在创建一个新的 WP 帖子，并在任何给定的测试方法完成后删除它。由于我们将`true`传递给`wp_delete_post`，它也将[清理我们在此过程中创建的任何附加帖子元。](https://codex.wordpress.org/Function_Reference/wp_delete_post)

之后，我们可以添加特定于方法的测试来覆盖我们所有的案例。

```
public function test_getEmotionalizedContent_shouldBeAngryWhenTagIsApplied()
{
    wp_set_post_tags($this->testPostId, 'angry', true);
    $this->assertEquals(
        getEmotionalizedContent($this->testPostId),
        "THIS IS JUST SOME SAMPLE POST CONTENT."
    );
}

public function test_getEmotionalizedContent_shouldBeExcitedWhenTagIsApplied()
{
    wp_set_post_tags($this->testPostId, 'excited', true);
    $this->assertStringEndsWith(
        "!", 
        getEmotionalizedContent($this->testPostId)
    );
}

public function test_getEmotionalizedContent_shouldBeObnoxiousWhenTagIsApplied()
{
    wp_set_post_tags($this->testPostId, 'obnoxious', true);
    $this->assertEquals(
        getEmotionalizedContent($this->testPostId), 
        "This 👏 is 👏 just 👏 some 👏 sample 👏 post 👏 content."
    );
}

public function test_getEmotionalizedContent_shouldBeEverythingWhenTagAreApplied()
{
    wp_set_post_tags($this->testPostId, 'obnoxious', true);
    wp_set_post_tags($this->testPostId, 'excited', true);
    wp_set_post_tags($this->testPostId, 'angry', true);

    $result = getEmotionalizedContent($this->testPostId);

    $this->assertStringStartsWith(
        "THIS 👏 IS 👏 JUST 👏 SOME 👏 SAMPLE 👏 POST 👏 CONTENT",
        $result
    );
    $this->assertStringEndsWith("!", $result);
} 
```

Enter fullscreen mode Exit fullscreen mode

再次运行`vendor/bin/phpunit`，您应该看到一些成功:

```
root@bc37c28f5d63:/var/www/html/wp-content/plugins/unit-testing-plugin# vendor/bin/phpunitPHPUnit 8.2.3 by Sebastian Bergmann and contributors.

.... 4 / 4 (100%)

Time: 3.44 seconds, Memory: 32.00 MB

OK (4 tests, 5 assertions) 
```

Enter fullscreen mode Exit fullscreen mode

Gr8 工作。但是随着测试的增长，很可能需要在几个不同的类中与帖子进行交互。所以，让我们将一些特定于 WordPress 的东西抽象到它自己的类中，这个类扩展了`\PHPUnit\Framework\TestCase`。

```
<?php 

namespace PluginTests;

class PostTestCase extends \PHPUnit\Framework\TestCase
{
    public $testPostId;

    public function setUp(): void
    {
        $this->testPostId = wp_insert_post([
            'post_title' => 'Sample Post',
            'post_content' => 'This is just some sample post content.'
        ]);
    }

    public function tearDown(): void
    {
        wp_delete_post($this->testPostId, true);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，在我们的测试类中，我们可以扩展这个新的助手类:

```
-class SomePostTest extends \PHPUnit\Framework\TestCase
+class SomePostTest extends \PluginTests\PostTestCase 
```

Enter fullscreen mode Exit fullscreen mode

感谢我们之前做的自动加载配置，我们不需要包含新的助手类——它会自动发生，看不见。因此，从现在开始，我们可以轻松地访问`$this->testPostId`和我们选择在`PostTestCase`中提供的任何其他资源，而不是在我们单独的测试类中处理所有的设置和清理。

随着需求的增加，您可以创建您自己的不同的测试用例，并根据您需要的资源类型进行扩展。例如，为了方便测试 WordPress 用户对象的代码，拥有一个`\PluginTests\UserTestCase`可能是好的。

## 是啊，取舍是存在的

这整个方法的价值是(希望)一条更快的途径来开始在 WordPress 中编写测试。也就是说，随之而来的还有一些权衡。例如:

*   如果你在运行测试时不小心清理你创建的数据，你的本地数据库可能会变得相当混乱。在许多情况下，最好手动创建一个测试数据库，并在测试时将应用程序指向该数据库。我肯定有一些聪明的编程方法来完成我解释的设置，但我还没有探索那些水域。
*   你错过了 WordPress 推荐的测试套件设置所包含的特性。是的，如果能够利用这些就好了，但是我发现我不需要太多就可以使用这种简化的方法为我的代码建立有价值的测试。这在任何时候都可能改变，但在那之前，我很乐意牺牲这一点。

## 反馈:你得到了什么？

如果你有一些技巧来改进这整个设置而不失去它的相对简单性，分享它们吧！无论如何，希望这是有帮助的。