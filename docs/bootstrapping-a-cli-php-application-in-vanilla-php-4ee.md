# 在普通 PHP 中引导 CLI PHP 应用程序

> 原文：<https://dev.to/erikaheidi/bootstrapping-a-cli-php-application-in-vanilla-php-4ee>

## 简介

众所周知，PHP 在 web 应用程序和 CMS 中很受欢迎，但是很多人不知道的是，PHP 也是一种很好的语言，可以用来构建不需要 web 服务器的命令行应用程序。它的易用性和熟悉的语法使它成为免费工具和小应用程序的低门槛语言，例如，这些小应用程序可能通过 Crontab 与 API 通信或执行预定任务，但不需要向外部用户公开。

当然，您可以构建一个单文件的 PHP 脚本来满足您的需求，这对于一些小事情来说可能相当不错；但是这使得将来维护、扩展或重用这些代码变得非常困难。当构建命令行应用程序时，可以应用相同的 web 开发原则，除了我们不再使用前端——耶！此外，外部用户无法访问该应用程序，这增加了安全性并为实验创造了更多空间。

我有点讨厌 web 应用程序和最近围绕前端构建的复杂性，所以在命令行中摆弄 PHP 对我个人来说非常新鲜。在这个帖子/系列中，我们将一起构建一个极简/无依赖性的 cli 应用程序包(想想一个小框架)- **minicli** -它可以作为 PHP 中实验性 CLI 应用程序的基础。

*PS。:如果你需要的只是一个`git clone`、[请到这里](https://github.com/erikaheidi/minicli)。*

这是**建筑迷你**系列的**部分 1** 。

### 先决条件

为了遵循本教程，您需要在本地机器或开发服务器上安装`php-cli`，以及用于生成自动加载文件的 [Composer](https://getcomposer.org) 。

## 1。设置目录结构&入口点

让我们首先创建主项目目录:

```
mkdir minicli
cd minicli 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们将为 CLI 应用程序创建入口点。这相当于现代 PHP web 应用程序上的一个`index.php`文件，其中一个入口点将请求重定向到相关的控制器。然而，由于我们的应用程序只有 CLI，我们将使用不同的文件名，并包括一些保护措施，不允许从 web 服务器执行。

使用您最喜欢的文本编辑器
打开一个名为`minicli`的新文件

```
vim minicli 
```

Enter fullscreen mode Exit fullscreen mode

你会注意到我们在这里没有包含一个`.php`扩展。因为我们在命令行上运行这个脚本，所以我们可以包含一个特殊的描述符来告诉您的 shell 程序我们正在使用 PHP 来执行这个脚本。

```
#!/usr/bin/php
<?php

if (php_sapi_name() !== 'cli') {
    exit;
}

echo "Hello World\n"; 
```

Enter fullscreen mode Exit fullscreen mode

第一行是应用程序 *shebang* 。它告诉运行这个脚本的 shell 使用`/usr/bin/php`作为代码的解释器。

用`chmod` :
使脚本可执行

```
chmod +x minicli 
```

Enter fullscreen mode Exit fullscreen mode

现在，您可以使用
运行应用程序

```
./minicli 
```

Enter fullscreen mode Exit fullscreen mode

您应该看到一个`Hello World`作为输出。

## 2。设置源目录和自动加载

为了便于在几个应用程序中重用这个框架，我们将创建**两个**源目录:

*   `app`:该命名空间将为特定应用模型和控制器保留。
*   核心框架类将使用这个名称空间，这些类可以在各种应用程序中重用。

使用
创建两个目录

```
mkdir app
mkdir lib 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们创建一个`composer.json`文件来设置*自动加载*。这将帮助我们在使用 PHP 中的类和其他面向对象的资源时更好地组织我们的应用程序。

在文本编辑器中创建一个新的`composer.json`文件，包含以下内容:

```
{
  "autoload": {
    "psr-4": {
      "Minicli\\": "lib/",
      "App\\": "app/"
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

保存并关闭文件后，运行以下命令来设置自动加载文件:

```
composer dump-autoload 
```

Enter fullscreen mode Exit fullscreen mode

为了测试自动加载是否按预期工作，我们将创建第一个类。这个类将代表应用程序对象，负责处理命令执行。我们将保持它的简单，并将其命名为`App`。

使用你选择的文本编辑器
，在你的`lib`文件夹中创建一个新的`App.php`文件

```
vim lib/App.php 
```

Enter fullscreen mode Exit fullscreen mode

`App`类实现了一个`runCommand`方法，取代了我们之前在`minicli`可执行文件中设置的“Hello World”代码。
我们稍后将修改这个方法，使它可以处理几个命令。现在，它将使用执行脚本时传递的参数输出一个“Hello $name”文本；如果没有参数被传递，它将使用`world`作为`$name`变量的默认值。

将以下内容插入到您的`App.php`文件中，完成后保存并关闭文件:

```
<?php

namespace Minicli;

class App
{
    public function runCommand(array $argv)
    {
        $name = "World";
        if (isset($argv[1])) {
            $name = $argv[1];
        }

        echo "Hello $name!!!\n";
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在转到您的`minicli`脚本，用下面的代码替换当前内容，我们稍后会解释:

```
#!/usr/bin/php
<?php

if (php_sapi_name() !== 'cli') {
    exit;
}

require __DIR__ . '/vendor/autoload.php';

use Minicli\App;

$app = new App();
$app->runCommand($argv); 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们需要自动生成的`autoload.php`文件，以便在创建新对象时自动包含类文件。创建了`App`对象后，我们调用`runCommand`方法，传递包含运行脚本时使用的所有参数的全局`$argv`变量。`$argv`变量是一个数组，其中第一个位置(0)是脚本的名称，随后的位置由传递给命令调用的额外参数占据。这是一个从命令行执行的 PHP 脚本中可用的预定义变量[。](https://www.php.net/manual/en/reserved.variables.argv.php)

现在，要测试一切是否按预期运行，运行:

```
./minicli your-name 
```

Enter fullscreen mode Exit fullscreen mode

您应该会看到下面的输出:

```
Hello your-name!!! 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果不向脚本传递任何额外的参数，它应该会打印:

```
Hello World!!! 
```

Enter fullscreen mode Exit fullscreen mode

## 3。创建输出助手

因为命令行界面是纯文本的，所以有时很难识别来自应用程序的错误或警告消息，或者很难以更易于阅读的方式格式化数据。我们将把其中的一些任务外包给一个助手类，它将处理终端的输出。

使用您选择的文本编辑器:
在`lib`文件夹中创建一个新类

```
vim lib/CliPrinter.php 
```

Enter fullscreen mode Exit fullscreen mode

下面的类定义了三个公共方法:一个输出消息的基本`out`方法；一个`newline`方法打印一个新行；还有一种`display`方法，结合了这两种方法来强调文本，用新行换行。稍后我们将扩展这个类，以包含更多的格式选项。

```
<?php

namespace Minicli;

class CliPrinter
{
    public function out($message)
    {
        echo $message;
    }

    public function newline()
    {
        $this->out("\n");
    }

    public function display($message)
    {
        $this->newline();
        $this->out($message);
        $this->newline();
        $this->newline();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们更新`App`类来使用 CliPrinter 辅助类。我们将创建一个名为`$printer`的属性，它将引用一个`CliPrinter`对象。该对象是在`App`构造函数方法中创建的。然后我们将创建一个`getPrinter`方法，并在`runCommand`方法中使用它来显示我们的消息，而不是直接使用`echo`:

```
<?php

namespace Minicli;

class App
{
    protected $printer;

    public function __construct()
    {
        $this->printer = new CliPrinter();
    }

    public function getPrinter()
    {
        return $this->printer;
    }

    public function runCommand($argv)
    {
        $name = "World";
        if (isset($argv[1])) {
            $name = $argv[1];
        }

        $this->getPrinter()->display("Hello $name!!!");
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在使用
再次运行应用程序

```
./minicli your_name 
```

Enter fullscreen mode Exit fullscreen mode

您应该得到这样的输出(消息周围有换行符):

```
 Hello your_name!!! 
```

Enter fullscreen mode Exit fullscreen mode

在下一步中，我们将把命令逻辑移到`App`类之外，以便在需要时更容易包含新命令。

## 4。创建命令注册表

我们现在将重构`App`类，通过一个通用的`runCommand`方法和一个*命令注册表*来处理多个命令。新命令将被注册，就像在一些流行的 PHP web 框架中定义的*路由*一样。

更新后的`App`类现在将包含一个新属性，一个名为`command_registry`的数组。方法`registerCommand`将使用这个变量将应用程序命令存储为由名称标识的匿名函数。

`runCommand`方法现在检查`$argv[1]`是否被设置为注册的命令名。如果没有设置命令，默认情况下会尝试执行一个`help`命令。如果没有找到有效的命令，它将打印一条错误消息。

这是经过这些更改后更新后的`App.php`类的样子。用下面的代码替换您的`App.php`文件的当前内容:

```
<?php

namespace Minicli;

class App
{
    protected $printer;

    protected $registry = [];

    public function __construct()
    {
        $this->printer = new CliPrinter();
    }

    public function getPrinter()
    {
        return $this->printer;
    }

    public function registerCommand($name, $callable)
    {
        $this->registry[$name] = $callable;
    }

    public function getCommand($command)
    {
        return isset($this->registry[$command]) ? $this->registry[$command] : null;
    }

    public function runCommand(array $argv = [])
    {
        $command_name = "help";

        if (isset($argv[1])) {
            $command_name = $argv[1];
        }

        $command = $this->getCommand($command_name);
        if ($command === null) {
            $this->getPrinter()->display("ERROR: Command \"$command_name\" not found.");
            exit;
        }

        call_user_func($command, $argv);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们将更新我们的`minicli`脚本并注册两个命令:`hello`和`help`。这些将被注册为我们的`App`对象中的匿名函数，使用新创建的`registerCommand`方法。

复制更新的`minicli`脚本并更新您的文件:

```
#!/usr/bin/php
<?php

if (php_sapi_name() !== 'cli') {
    exit;
}

require __DIR__ . '/vendor/autoload.php';

use Minicli\App;

$app = new App();

$app->registerCommand('hello', function (array $argv) use ($app) {
    $name = isset ($argv[2]) ? $argv[2] : "World";
    $app->getPrinter()->display("Hello $name!!!");
});

$app->registerCommand('help', function (array $argv) use ($app) {
    $app->getPrinter()->display("usage: minicli hello [ your-name ]");
});

$app->runCommand($argv); 
```

Enter fullscreen mode Exit fullscreen mode

现在您的应用程序有两个工作命令:`help`和`hello`。要测试它，运行:

```
./minicli help 
```

Enter fullscreen mode Exit fullscreen mode

这将打印:

```
 usage: minicli hello [ your-name ] 
```

Enter fullscreen mode Exit fullscreen mode

现在用
测试`hello`命令

```
./minicli hello your_name 
```

Enter fullscreen mode Exit fullscreen mode

```
 Hello your_name!!! 
```

Enter fullscreen mode Exit fullscreen mode

现在，您已经有了一个使用极简结构的 CLI 应用程序，它将作为实现更多命令和功能的基础。

这就是此时你的目录结构的样子:

```
.
├── app
├── lib
│   ├── App.php
│   └── CliPrinter.php
├── vendor
│   ├── composer
│   └── autoload.php
├── composer.json
└── minicli 
```

Enter fullscreen mode Exit fullscreen mode

在本系列的下一部分中，我们将重构`minicli`以使用**命令控制器**，将命令逻辑移动到特定于应用程序的名称空间中的专用类。下次见！