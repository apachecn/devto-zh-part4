# 构建 minicli:实现命令控制器

> 原文：<https://dev.to/erikaheidi/php-in-the-command-line-implementing-command-controllers-13lh>

## 简介

[MVC](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller) (模型、视图、控制器)模式在 web 应用中非常流行。控制器负责根据 web 应用程序请求的端点来处理代码执行。CLI 应用程序没有端点，但是我们可以通过*命令控制器*路由命令执行来实现类似的工作流。

在本系列的第一篇教程[中，我们为命令行界面(CLI)引导了一个 PHP 应用程序，使用单一入口点并通过匿名函数注册命令。在这个新教程中，我们将重构`minicli`来使用命令控制器。](https://dev.to/erikaheidi/bootstrapping-a-cli-php-application-in-vanilla-php-4ee)

这是[楼宇微缩](https://dev.to/erikaheidi/bootstrapping-a-cli-php-application-in-vanilla-php-4ee)系列的**部分 2** 。

### 入门前

你需要`php-cli`和[作曲家](https://getcomposer.org/)来跟随这个教程。

如果你还没有完成这个系列的第一部分[，你可以下载](https://dev.to/erikaheidi/bootstrapping-a-cli-php-application-in-vanilla-php-4ee) [erikaheidi/minicli](https://github.com/erikaheidi/minicli) 的`0.1.0`版本来引导你的设置:

```
wget https://github.com/erikaheidi/minicli/archive/0.1.0.zip
unzip 0.1.0.zip
cd minicli 
```

Enter fullscreen mode Exit fullscreen mode

然后，运行 Composer 来设置自动加载。这不会安装任何包，因为`minicli`没有依赖项。

```
composer dump-autoload 
```

Enter fullscreen mode Exit fullscreen mode

使用
运行应用程序

```
php minicli 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
chmod +x minicli
./minicli 
```

Enter fullscreen mode Exit fullscreen mode

## 1。将命令注册外包给一个`CommandRegistry`类

为了开始我们的重构，我们将创建一个新的类来处理为应用程序注册和定位命令的工作。这项工作目前由`App`类处理，但是我们将把它外包给一个名为`CommandRegistry`的类。

使用您选择的编辑器创建新类。为了简单起见，在本教程中我们将使用`nano` :

```
nano lib/CommandRegistry.php 
```

Enter fullscreen mode Exit fullscreen mode

将以下内容复制到您的`CommandRegistry`类:

```
<?php

namespace Minicli;

class CommandRegistry
{
    protected $registry = [];

    public function registerCommand($name, $callable)
    {
        $this->registry[$name] = $callable;
    }

    public function getCommand($command)
    {
        return isset($this->registry[$command]) ? $this->registry[$command] : null;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

*注意:`getCommand`方法使用三元运算符作为 if/else 的简写。如果没有找到命令，它将返回`null`。*

完成后保存并关闭文件。

现在，编辑文件`App.php`并用下面的代码替换当前内容，该代码包含了用于注册命令的`CommandRegistry`类:

```
<?php

namespace Minicli;

class App
{
    protected $printer;

    protected $command_registry;

    public function __construct()
    {
        $this->printer = new CliPrinter();
        $this->command_registry = new CommandRegistry();
    }

    public function getPrinter()
    {
        return $this->printer;
    }

    public function registerCommand($name, $callable)
    {
        $this->command_registry->register($name, $callable);
    }

    public function runCommand(array $argv = [])
    {
        $command_name = "help";

        if (isset($argv[1])) {
            $command_name = $argv[1];
        }

        $command = $this->command_registry->getCommand($command_name);
        if ($command === null) {
            $this->getPrinter()->display("ERROR: Command \"$command_name\" not found.");
            exit;
        }

        call_user_func($command, $argv);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你现在用`./minicli`运行应用程序，应该不会有什么变化，你仍然可以运行`hello`和`help`命令。

## 2。实现命令控制器

现在我们将进一步重构命令，将特定的命令过程转移到专用的`CommandController`类中。

### 2.1 创建一个`CommandController`模型

我们需要做的第一件事是建立一个可以被几个命令继承的抽象模型。这将允许我们在通过需要由子(具体)类实现的抽象方法来实施一组特性时，拥有一些默认的实现。

当用户在命令行上调用该命令时，该模型应该定义至少一个强制方法，由给定具体的`CommandController`上的`App`类调用。

在文本编辑器中打开一个新文件:

```
nano lib/CommandController.php 
```

Enter fullscreen mode Exit fullscreen mode

将以下内容复制到该文件中。这就是我们最初的`CommandController`抽象类应该看起来的样子:

```
<?php

namespace Minicli;

abstract class CommandController
{
    protected $app;

    abstract public function run($argv);

    public function __construct(App $app)
    {
        $this->app = $app;
    }

    protected function getApp()
    {
        return $this->app;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

任何从`CommandController`继承的类都将继承`getApp`方法，但是它需要实现一个`run`方法并处理命令执行。

### 2.2 创建具体的命令控制器

现在我们将创建我们的第一个命令控制器具体类:`HelloController`。这个类将替换当前的`hello`命令的定义，从一个匿名函数到一个`CommandController`对象。

还记得我们如何在 Composer 文件中创建两个独立的名称空间吗，一个用于框架，一个用于应用程序？因为这段代码特定于正在开发的应用程序，所以我们现在将使用`App`名称空间。

首先，在`app`名称空间目录:
中创建一个名为`Command`的新文件夹

```
mkdir app/Command 
```

Enter fullscreen mode Exit fullscreen mode

在文本编辑器中打开一个新文件:

```
 nano app/Command/HelloController.php 
```

Enter fullscreen mode Exit fullscreen mode

将以下内容复制到您的控制器。新的`HelloController`类应该是这样的:

```
<?php

namespace App\Command;

use Minicli\CommandController;

class HelloController extends CommandController
{
    public function run($argv)
    {
        $name = isset ($argv[2]) ? $argv[2] : "World";
        $this->getApp()->getPrinter()->display("Hello $name!!!");
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这里没什么事情。我们重用了之前的代码，但是现在它被放在一个继承自`CommandController`的单独的类中。现在可以通过从父抽象类`CommandController`继承的方法`getApp`访问`App`对象。

### 2.3 更新`CommandRegistry`使用控制器

我们已经为基于继承的命令控制器定义了一个简单的架构，但是我们仍然需要更新`CommandRegistry`类来处理这些变化。

能够将命令分成它们自己的类对于可维护性来说是非常好的，但是对于简单的命令，您可能仍然更喜欢使用匿名函数。

下面的代码实现了命令控制器的注册，并与前面使用匿名函数定义命令的方法保持兼容。使用您选择的编辑器
打开`CommandRegistry.php`文件

```
nano lib/CommandRegistry.php 
```

Enter fullscreen mode Exit fullscreen mode

用下面的代码更新`CommandRegistry`类的当前内容:

```
<?php

namespace Minicli;

class CommandRegistry
{
    protected $registry = [];

    protected $controllers = [];

    public function registerController($command_name, CommandController $controller)
    {
        $this->controllers = [ $command_name => $controller ];
    }

    public function registerCommand($name, $callable)
    {
        $this->registry[$name] = $callable;
    }

    public function getController($command)
    {
        return isset($this->controllers[$command]) ? $this->controllers[$command] : null;
    }

    public function getCommand($command)
    {
        return isset($this->registry[$command]) ? $this->registry[$command] : null;
    }

    public function getCallable($command_name)
    {
        $controller = $this->getController($command_name);

        if ($controller instanceof CommandController) {
            return [ $controller, 'run' ];
        }

        $command = $this->getCommand($command_name);
        if ($command === null) {
            throw new \Exception("Command \"$command_name\" not found.");
        }

        return $command;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

因为我们现在已经在应用程序中注册了命令控制器和简单的回调函数，所以我们已经实现了一个名为`getCallable`的方法，该方法将负责计算在调用命令时应该调用哪个代码。如果找不到命令，这个方法会抛出一个异常。按照我们的实现方式，命令控制器总是优先于通过匿名函数注册的单个命令。

替换完旧代码后，保存并关闭文件。

### 2.4 更新`App`类

我们仍然需要更新`App`类来处理所有最近的变化。

打开包含`App`类的文件:

```
nano lib/App.php 
```

Enter fullscreen mode Exit fullscreen mode

用以下代码替换`App.php`文件的当前内容:

```
<?php

namespace Minicli;

class App
{
    protected $printer;

    protected $command_registry;

    public function __construct()
    {
        $this->printer = new CliPrinter();
        $this->command_registry = new CommandRegistry();
    }

    public function getPrinter()
    {
        return $this->printer;
    }

    public function registerController($name, CommandController $controller)
    {
        $this->command_registry->registerController($name, $controller);
    }

    public function registerCommand($name, $callable)
    {
        $this->command_registry->registerCommand($name, $callable);
    }

    public function runCommand(array $argv = [], $default_command = 'help')
    {
        $command_name = $default_command;

        if (isset($argv[1])) {
            $command_name = $argv[1];
        }

        try {
            call_user_func($this->command_registry->getCallable($command_name), $argv);
        } catch (\Exception $e) {
            $this->getPrinter()->display("ERROR: " . $e->getMessage());
            exit;
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们实现了一个方法，允许用户在实例化一个 App 对象后注册命令控制器:`registerController`。这个方法将命令注册外包给`CommandRegistry`对象。然后，我们将`runCommand`方法更新为使用`getCallable`，在 try / catch 块中捕获一个可能的异常。

完成编辑后，保存并关闭文件。

### 2.5 注册`HelloController`命令控制器

`minicli`脚本仍然使用通过匿名函数定义命令的老方法。我们现在将更新这个文件以使用我们新的`HelloController`命令控制器，但是我们将保持`help`命令注册和以前一样，注册为匿名函数。

打开`minicli`脚本:

```
nano minicli 
```

Enter fullscreen mode Exit fullscreen mode

这是更新后的`minicli`脚本现在的样子:

```
#!/usr/bin/php
<?php

if (php_sapi_name() !== 'cli') {
    exit;
}

require __DIR__ . '/vendor/autoload.php';

use Minicli\App;

$app = new App();

$app->registerController('hello', new \App\Command\HelloController($app));

$app->registerCommand('help', function (array $argv) use ($app) {
    $app->getPrinter()->display("usage: minicli hello [ your-name ]");
});

$app->runCommand($argv); 
```

Enter fullscreen mode Exit fullscreen mode

用新代码更新文件后，您应该能够像以前一样运行应用程序，并且它的行为应该完全相同:

```
./minicli 
```

Enter fullscreen mode Exit fullscreen mode

不同之处在于，现在您有两种创建命令的方式:通过向`registerCommand`注册一个匿名函数，或者通过创建一个继承自`CommandController`的控制器类。使用控制器类将使您的代码更有组织性和可维护性，但是您仍然可以使用匿名函数这种“捷径”来快速编写代码和简单的脚本。

## 结论&下一步

在这篇文章中，我们重构了`minicli`以支持在类中定义的命令，其架构使用了**命令控制器**。虽然这目前工作得很好，但是控制器*应该*能够处理不止一个命令；这将使我们更容易实现这样的命令结构:

```
command [ subcommand ] [ action ] [ params ]
command [ subcommand 1 ] [ subcommand n ] [ params ] 
```

Enter fullscreen mode Exit fullscreen mode

在本系列的下一部分，我们将重构`minicli`以支持**子命令**。

你怎么想呢?你将如何实现它？

干杯，再见！\,,/

-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-
本教程中用到的所有文件都可以在这里找到:[erikaheidi/minic Li:v 0 . 1 . 2](https://github.com/erikaheidi/minicli/tree/0.1.2)