# 如何编写 Magento 2 控制台命令

> 原文：<https://dev.to/restoreddev/how-to-write-a-magento-2-console-command-1gl8>

在 Magento 2 中，该平台引入了一个名为`bin/magento`的新命令行界面，它包含在每个安装中。大多数 Magento 开发人员都很熟悉它，因为它使得在安装时执行普通任务变得非常容易，比如清除缓存和安装数据库。您可能不知道的是，您可以向 Magento CLI 添加自定义命令，以允许您运行自己的代码。我已经使用这个特性创建了定制的 cron 作业，这些作业可以由 OS cron 而不是 Magento 的 cron 系统来执行。Magento 的 CLI 基于 PHP 生态系统中常用的 [Symfony 控制台](https://symfony.com/doc/current/components/console.html)包。例如，Laravel 将他们的`artisan`命令基于 Symfony 控制台。在 Magento 2 中，你可以创建自定义的 Symfony 控制台命令，并将它们包含在 Magento 的 CLI 中。为了帮助其他开发人员开始使用控制台命令，我想提供这个快速教程。

我假设你已经安装了 Magento 2，准备用于开发。要了解如何设置 Magento，请查看 [Magento 的 devdocs 帖子](https://devdocs.magento.com/guides/v2.3/install-gde/bk-install-guide.html)获取安装说明。这个教程我用的是 Magento 社区 2.3.1。

### 1。制作一个模块

我们需要在`app/code`中创建自己的定制模块。我正在调用我的模块`Restoreddev_Console`。对于每个模块，我们需要一个`registration.php`和`module.xml`文件。

`app/code/Restoreddev/Console/registration.php`

```
<?php

use \Magento\Framework\Component\ComponentRegistrar;

ComponentRegistrar::register(ComponentRegistrar::MODULE, 'Restoreddev_Console', __DIR__); 
```

`app/code/Restoreddev/Console/etc/module.xml`

```
<?xml version="1.0"?>
<config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:framework:Module/etc/module.xsd">
    <module name="Restoreddev_Console" />
</config> 
```

现在你可以运行`php bin/magento setup:upgrade`将你的定制模块安装到 Magento 的数据库中。

### 2。创建控制台命令类

接下来，我们可以创建一个实现 Symfony 命令的类，它将在我们的命令执行时运行。我想创建一个自定义命令来清除`generated`目录，类似于`cache:flush`的工作方式。将此类添加到您的模块中:

`app/code/Restoreddev/Console/Console/Command/GeneratedFlushCommand.php`

```
<?php

namespace Restoreddev\Console\Console\Command;

use Symfony\Component\Console\Command\Command;
use Magento\Framework\Filesystem\DirectoryList;
use Magento\Framework\Filesystem\Io\File as FileIo;
use Symfony\Component\Console\Input\InputInterface;
use Symfony\Component\Console\Output\OutputInterface;

class GeneratedFlushCommand extends Command
{
    protected $fileIo;
    protected $directoryList;

    public function __construct(
        FileIo $fileIo,
        DirectoryList $directoryList
    ) {
        parent::__construct();

        $this->fileIo = $fileIo;
        $this->directoryList = $directoryList;
    }

    protected function configure()
    {
        $this->setName('generated:flush');
        $this->setDescription('Deletes code in generated folder');

        parent::configure();
    }

    protected function execute(InputInterface $input, OutputInterface $output)
    {
        $path = $this->directoryList->getPath('generated');
        $dirs = glob("$path/*", GLOB_ONLYDIR);
        foreach ($dirs as $dir) {
            $this->fileIo->rmdir($dir, $recursive = true);
        }

        $output->writeln('Generated folder successfully flushed');
    }
} 
```

这堂课发生的事情很多，需要分段分析。

1.  您将看到该类扩展了`Symfony\Component\Console\Command\Command`。Symfony 父类为我们的类提供了包含在命令列表中的接口。
2.  在`__construct`方法中，我们可以像其他 Magento 类一样注入 Magento 依赖项。然而，您必须运行`parent::__construct();`才能正确实现这个类。
3.  `configure`方法允许您定义出现在 CLI 菜单中的命令名和描述。
4.  `execute`方法包含执行 CLI 命令时将运行的代码。该方法接收两个对象:`$input`(包含任何控制台参数)和`$output`(允许您向终端发送文本)。我使用 Magento 的类来获取`generated`目录并删除其中的任何目录。最后，我使用`$output->writeln()`向用户发送一条成功消息。

### 3。Magento 中的注册命令

最后，我们需要在 Magento 的依赖注入系统中注册这个命令。在您的模块中创建下面的`di.xml`文件。

`app/code/Restoreddev/Console/etc/di.xml`

```
<?xml version="1.0"?>
<config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:framework:ObjectManager/etc/config.xsd">
    <type name="Magento\Framework\Console\CommandListInterface">
        <arguments>
            <argument name="commands" xsi:type="array">
                <item name="generatedFlushCommand" xsi:type="object">Restoreddev\Console\Console\Command\GeneratedFlushCommand</item>
            </argument>
        </arguments>
    </type>
</config> 
```

在`di.xml`中，我们以`CommandListInterface`为目标，并为其参数添加了一个额外的命令。现在你可以刷新 Magento 的缓存(`php bin/magento cache:flush`)并运行`php bin/magento`。你会看到一个名为`generated:flush`的新命令。如果您执行它`php bin/magento generated:flush`，那么您将看到`generated`文件夹中的所有子目录都被删除了。

我希望这个快速教程能帮助你了解 Magento 2 中一个简洁的新特性。如果你想看另一个具体的 Magento 教程，请在评论中告诉我！