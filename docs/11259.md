# 如何用 PHP 和基本框架创建一个文件监视器

> 原文：<https://dev.to/na2axl/how-to-create-a-file-watcher-with-php-and-the-elementary-framework-1po>

# 简介

欢迎大家来到第二个关于[基本框架](https://github.com/ElementaryFramework)的操作指南(对于那些错过了第一个操作指南的人，请查看这里的)。

在这篇文章中，我们将讨论一个**文件监视器**的创建，这是一个控制台应用程序，它将*监视目录中的*文件，并向我们报告对这些文件所做的任何更改(删除、创建、修改)，它是使用我的框架的一部分 [FireFS](https://github.com/ElementaryFramework/FireFS) 用 PHP 语言编写的。

> 创建一个文件监视器，这只是更多人之间发生冲突的可能性。

# 先决条件

开始之前，请确保您已经:

*   PHP IDE 或编辑器:你可以下载 Visual Studio 代码，这是一个免费的强大的代码编辑器，可以支持许多语言(包括 PHP)。
*   Composer: Composer 是 PHP 项目的依赖管理器。你可以在 https://getcomposer.org/下载

# 第 1 部分-准备项目

我们要做的第一件事是创建我们的新项目。用您想要的名称创建一个新文件夹，并在其中打开一个终端。

FireFS 是一个 composer 包，所以我们首先需要准备我们的项目来使用 composer。在之前打开的终端中，键入:

```
composer init 
```

这将使用 composer 支持初始化我们的项目。将初始化过程进行到底。

现在，在同一个终端中，我们可以用命令安装 FireFS:

```
composer require elementaryframework/fire-fs 
```

并等待该过程完成，这将下载 FireFS 项目的源代码并将其安装在您自己的系统中。安装成功后，再次输入:

```
composer dump-autoload -o 
```

这将生成一个*优化的*自动加载器文件(对生产有用)。对于正常的自动加载文件，只需省略`-o`开关。

安装 FireFS 后，我们现在可以创建命令行工具来查看我们的文件。在项目文件夹中创建一个名为`watch`的文件，并将其插入:

```
#!/usr/bin/env php

<?php

// Require composer autoload file
require "./vendor/autoload.php";

// Print a message to the console
print "Hello world, this is a file watcher!"; 
```

我们在做什么？

*   我们首先需要来自 composer 的 autoloader 文件
*   我们在控制台输出中打印一条消息

要测试一切是否正常，只需在终端中键入(虽然工作目录与我们的项目相同):

```
php watch 
```

控制台将打印文本" **Hello world，这是一个文件守望者！**"退出前。

> 注意文件名是**手表**(没有任何扩展名)，而不是【watch.php】T2

# 第二部分——我们的文件守望者

用 FireFS 构建一个文件监视器非常简单，我们只需要创建一个新的`FileSystemWatcher`对象，遵循构建模式并启动它，很简单，对吗？

## `FireFS`物体

在 FireFS 中，`FireFS`类的实例代表一个*文件系统*，管理给定**根目录**中的每个文件和文件夹。当创建一个文件监视器时，我们首先要创建一个文件系统:

```
// We use the FireFS class
use ElementaryFramework\FireFS\FireFS;

// We create a new file system on the current directory
$fs = new FireFS(); 
```

这将在与我们的`watch`文件相同的路径下创建一个文件系统(要更改根路径，只需将您的首选路径定义为 FireFS 类构造函数的第一个参数)。

## 被关注的文件夹

现在，我们必须创建将被监视的文件夹。我们可以通过使用强大的 FireFS API 让我们的控制台应用程序自己完成这项工作:

```
// Check if the directory to watch exists
if (!$fs->exists("files_to_watch")) {
    // If not, create the directory
    $fs->mkdir("files_to_watch");
} 
```

这将创建目录`files_to_watch`,如果它在我们的文件监视器每次运行时不存在的话。

## `IFileSystemListener`物体

在使用我们的文件监视器之前，我们必须创建一个实现`IFileSystemListener`接口的类。这个类被观察者用来在*观察*事件之后执行特定的动作(创建、删除、更新)。

```
// We use the IFileSystemWatcher interface
use ElementaryFramework\FireFS\Listener\IFileSystemListener;

// We use the FileSystemEvent class
use ElementaryFramework\FireFS\Events\FileSystemEvent;

// Our listener
class WatcherListener implements IFileSystemListener
{
    /**
     * Action executed on any event.
     * The returned boolean will define if the
     * specific event handler (onCreated, onModified, onDeleted)
     * have to be called after this call.
     */
    public function onAny(FileSystemEvent $event): bool
    {
        $eventType = $event->getEventType();
        $date = date("d/m/Y H:i:s");

        if ($eventType === FileSystemEvent::EVENT_UNKNOWN) {
            return true;
        }

        switch ($eventType) {
            case FileSystemEvent::EVENT_CREATE:
                print "{$date} -  [Created] {$event->getPath()}\n";
                break;

            case FileSystemEvent::EVENT_MODIFY:
                print "{$date} -  [Updated] {$event->getPath()}\n";
                break;

            case FileSystemEvent::EVENT_DELETE:
                print "{$date} -  [Deleted] {$event->getPath()}\n";
                break;
        }

        return false;
    }

    /**
     * Action executed when a file/folder is created.
     */
    public function onCreated(FileSystemEvent $event)
    { }

    /**
     * Action executed when a file/folder is updated.
     */
    public function onModified(FileSystemEvent $event)
    { }

    /**
     * Action executed when a file/folder is deleted.
     */
    public function onDeleted(FileSystemEvent $event)
    { }
} 
```

## `FileSystemWatcher`物体

一旦我们有了一个文件系统对象，并且知道了我们想要监视的目录(或文件)，我们就可以创建文件监视器:

```
// We use the FileSystemWatcher class
use ElementaryFramework\FireFS\Watcher\FileSystemWatcher;

// Create the file watcher
$watcher = new FileSystemWatcher($fs); 
```

下一步是配置我们的观察器。配置是通过调用一些方法来完成的:

*   `setListener(IFileSystemListener)`:定义用于对每个文件更新执行特定动作的`IFileSystemListener`实例；
*   `setPath(string)`:定义要查看的目录/文件的路径；
*   `setRecursive(bool)`:定义监视器是否递归监视文件(仅当被监视实体为目录时有效)；
*   `addPattern(Regex)`:添加文件观看模式；
*   `addExcludePattern(Regex)`:添加文件排除模式；
*   `setPattern(array)`:设置一组文件观察模式。这将覆盖任何现有模式；
*   `setExcludePattern(array)`:设置要排除的文件模式数组。这将覆盖任何现有模式；
*   `setWatchInterval(int)`:设置观察器处理一个*观察*事件的时间间隔，单位为毫秒。

因此，我们将这样构建我们的观察器:

```
$watcher
    ->setListener(new WatcherListener)
    ->setRecursive(true)
    ->setPath("./files_to_watch")
    ->setWatchInterval(250)
    ->build(); // It's important to call build to validate the configuration 
```

> **重要的是要知道**:这个`FileSystemWatcher`类有一些默认的排除模式，在这里[查看一下](https://github.com/ElementaryFramework/FireFS/blob/master/src/FireFS/Watcher/FileSystemWatcher.php#L97)

这时候一切都定了！现在要做的最后一件事是启动观察器:

```
// Start the file watcher
$watcher->start(); 
```

## 完整的`watch`文件

我们刚刚创建完我们的文件观察者，这里有完整的文件内容:

```
#!/usr/bin/env php

<?php

// Require composer autoloader
require_once __DIR__ . '/vendor/autoload.php';

// We use the FireFS class
use ElementaryFramework\FireFS\FireFS;

// We use the IFileSystemWatcher interface
use ElementaryFramework\FireFS\Listener\IFileSystemListener;

// We use the FileSystemEvent class
use ElementaryFramework\FireFS\Events\FileSystemEvent;

// We use the FileSystemWatcher class
use ElementaryFramework\FireFS\Watcher\FileSystemWatcher;

// We create a new file system on the current directory
$fs = new FireFS();

// Check if the directory to watch exists
if (!$fs->exists("files_to_watch")) {
    // If not, create the directory
    $fs->mkdir("files_to_watch");
}

// Our listener
class WatcherListener implements IFileSystemListener
{
    /**
     * Action executed on any event.
     * The returned boolean will define if the
     * specific event handler (onCreated, onModified, onDeleted)
     * have to be called after this call.
     */
    public function onAny(FileSystemEvent $event): bool
    {
        $eventType = $event->getEventType();
        $date = date("d/m/Y H:i:s");

        if ($eventType === FileSystemEvent::EVENT_UNKNOWN) {
            return true;
        }

        switch ($eventType) {
            case FileSystemEvent::EVENT_CREATE:
                print "{$date} -  [Created] {$event->getPath()}\n";
                break;

            case FileSystemEvent::EVENT_MODIFY:
                print "{$date} -  [Updated] {$event->getPath()}\n";
                break;

            case FileSystemEvent::EVENT_DELETE:
                print "{$date} -  [Deleted] {$event->getPath()}\n";
                break;
        }

        return false;
    }

    /**
     * Action executed when a file/folder is created.
     */
    public function onCreated(FileSystemEvent $event)
    { }

    /**
     * Action executed when a file/folder is updated.
     */
    public function onModified(FileSystemEvent $event)
    { }

    /**
     * Action executed when a file/folder is deleted.
     */
    public function onDeleted(FileSystemEvent $event)
    { }
}

// Create the file watcher
$watcher = new FileSystemWatcher($fs);

$watcher
    ->setListener(new WatcherListener)
    ->setPath("./files_to_watch")
    ->setWatchInterval(250)
    ->build(); // It's important to call build to validate the configuration

// Start the file watcher
$watcher->start(); 
```

现在使用命令`php watch`运行这个文件，一个新的文件夹`files_to_watch`被直接创建，控制台等待文件改变。尝试在`files_to_watch`文件夹中创建、删除或编辑文件，你会在控制台中直接得到这些操作的通知！

```
php watch

26/07/2019 15:53:20  -  [Created]  D:\AliensGroup\Hyde\hyde-cli\files_to_watch\composer.json
26/07/2019 15:54:24  -  [Created]  D:\AliensGroup\Hyde\hyde-cli\files_to_watch\composer.lock
26/07/2019 15:54:24  -  [Created]  D:\AliensGroup\Hyde\hyde-cli\files_to_watch\hyde
26/07/2019 15:54:29  -  [Deleted]  D:\AliensGroup\Hyde\hyde-cli\files_to_watch\composer.lock
26/07/2019 15:54:47  -  [Updated]  D:\AliensGroup\Hyde\hyde-cli\files_to_watch\hyde 
```

# 结论

通过这篇文章，我们已经学习了如何用 PHP 和基本框架创建一个文件监视器。使用框架的 FireFS 模块，我们一步一步地看到了实现这样的功能是多么容易。现在，有了这些知识，你可以创建比简单的*事件报告*应用程序更多的东西，从你网络服务器上的 crons 到你自己的 Jekyll PHP 版本；-)，你拥有了更进一步所需的一切。

# 接下来呢？

*   浏览 GitHub 上的 [FireFS](https://github.com/ElementaryFramework/FireFS) 源代码——如果你喜欢的话给它打个星吧——；
*   检查其他[基本框架](https://github.com/ElementaryFramework)模块

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [基本框架](https://github.com/ElementaryFramework) / [火焰](https://github.com/ElementaryFramework/FireFS)

### 通过 PHP 轻松管理文件系统

<article class="markdown-body entry-content container-lg" itemprop="text">

[![FireFS Logo](img/df474dc2569e01de98d3afcf297f5d07.png)](https://raw.githubusercontent.com/ElementaryFramework/FireFS/master/assets/banner.png)

# 火灾

[![downloads](img/cc55d0045e02e5028316d9c1d1437289.png)](https://packagist.org/packages/elementaryframework/fire-fs)[![downloads](img/4e0e06dc1257fac72fd02ccb5cfc9239.png)](https://packagist.org/packages/elementaryframework/fire-fs)[![downloads](img/8f61329a5158e5573397f275f2e4ecd3.png)](https://github.com/ElementaryFramework/FireFS)[![downloads](img/d0972369dc13027ac1ae3b08c89abba8.png)T11】](https://github.com/ElementaryFramework/FireFS/blob/master/LICENSE)

通过 php 轻松管理您的文件系统

FireFS 是一个库，允许你安全方便地写/读/删除文件系统中的文件和文件夹。

它可以用于 *web* 应用程序，也可以用于*控制台*应用程序，没有任何要求。

## 例子

```
<?php
use ElementaryFramework\FireFS\FireFS
// Create a new file system instance at the given path
$fs = new FireFS("./app"); // /root/var/www/htdocs/app/
// Check if the path "/root/var/www/htdocs/aimg/" exists
if ($fs->exists("images")) {
    // Change the working directory to the images folder
    $fs->setWorkingDirectory("./images");

    // Create a new file in the working directory
    $fs->mkfile("./logo.png"); // /root/var/www/htdocs/aimg/logo.png

    // Read file from the file system root path
    $logo = $fs->read("logo.png"); // /root/var/www/htdocs/app/logo.png

    // Write into the created file
    $
```

…</article>

[View on GitHub](https://github.com/ElementaryFramework/FireFS)