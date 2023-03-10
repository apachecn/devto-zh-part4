# Craft CMS 中健壮的队列作业处理

> 原文：<https://dev.to/gaijinity/robust-queue-job-handling-in-craft-cms-1m0g>

# Craft CMS 中强大的队列作业处理

### Craft CMS 对长时间运行的 ning 任务使用队列作业。以下是如何使用独立异步队列运行器来避免队列作业停滞或失败的方法

安德鲁·韦尔奇

[![Craft cms yii2 async queue](img/cf258145d828fcfc03cbc8cdd614c25f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--i2RUdqBV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x675_crop_center-center_82_line/craft-cms-yii2-async-queue.jpg)

很少有人喜欢排队，但是它可以让每个人都得到有序、有效和公平的服务。

Craft CMS 也使用队列。任何冗长的任务都被打包成一个**作业**，作业被放入**队列**中按顺序执行。

[![Queue enqueue dequeue craft cms](img/4cebe28dc673954110ade3da874de30f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rq3gjC8e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x630_crop_center-center_100_line/queue-enqueue-dequeue-craft-cms.png)

用通俗的话来说，这是一种先进先出(FIFO)的数据结构，它按照我们所认为的自然顺序服务。

以下是一些在 Craft CMS 队列中作为作业结束的冗长任务:

*   更新
*   删除
*   等待图像转换的生成器
*   重新保存元素
*   更新
*   在 ed 队列作业中插入 cre

队列的存在是为了允许这些通常很长的作业在后台运行，而不会在它们执行时阻塞对 Craft CMS 控制面板(CP)的访问。

## 看他们怎么跑

现在，我们知道了什么类型的作业将在队列中结束，让我们看看这些作业是如何运行的。这最终成为理解队列作业的一个非常重要的信息。

[![Queue runners](img/8ca1ae9054dd69f955adf7d8eab8d927.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CnJjjeks--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x675_crop_center-center_82_line/queue-runners.jpg)

假设 Craft CMS 是建立在 [Yii2 框架](https://www.yiiframework.com/)之上，那么它通过在现有的 [Yii2 队列](https://github.com/yiisoft/yii2-queue)之上放置一个[队列面](https://github.com/craftcms/cms/blob/develop/src/queue/QueueInterface.php)来提升其寿命是有意义的。

它这样做是为了在 CP 中提供一个友好的状态用户界面。默认的工艺设置使用数据库(Db)驱动程序进行队列作业，并将它们存储在`queue`表中。

尽管有其他可用的队列驱动程序，但你很难看到使用 Craft CMS Db 驱动程序之外的任何东西会有什么好处。并且您会丢失 CP 中的上述状态界面。

<aside>Where queue jobs are stored and how they are run are com­plete­ly sep­a­rate things.</aside>

但是队列作业是如何运行的呢？方法如下:

*   当有人访问 CP 时
*   通过 web 请求

这就是 Craft CMS 中默认队列实现的问题所在。除非有人登录到 CP，否则队列作业根本不会运行，即使这样，它们也是通过 web 请求运行的。

下面是 Craft CMS `CP.js` JavaScript 的摘录，它启动了基于 web 的队列运行:

```
 runQueue: function() {
    if (!this.enableQueue) {
        return;
    }

    if (Craft.runQueueAutomatically) {
        Craft.queueActionRequest('queue/run', $.proxy(function(response, textStatus) {
            if (textStatus === 'success') {
                this.trackJobProgress(false, true);
            }
        }, this));
    }
    else {
        this.trackJobProgress(false, true);
    }
}, 
```

这里有一个[队列/运行控制器方法](https://github.com/craftcms/cms/blob/develop/src/controllers/QueueController.php#L57)，它最终通过 AJAX 获得了一个可执行文件:

```
 /**
 * Runs any waiting jobs.
 *
 * @return Response
 */
public function actionRun(): Response
{
    // Prep the response
    $response = Craft::$app->getResponse();
    $response->content = '1';
    // Make sure Craft is configured to run queues over the web
    if (!Craft::$app->getConfig()->getGeneral()->runQueueAutomatically) {
        return $response;
    }
    // Make sure the queue isn't already running, and there are waiting jobs
    $queue = Craft::$app->getQueue();
    if ($queue->getHasReservedJobs() || !$queue->getHasWaitingJobs()) {
        return $response;
    }
    // Attempt to close the connection if this is an Ajax request
    if (Craft::$app->getRequest()->getIsAjax()) {
        $response->sendAndClose();
    }
    // Run the queue
    App::maxPowerCaptain();
    $queue->run();
    return $response;
} 
```

他们正在尽全力确保基于网络的队列作业顺利运行，但能做的也就这么多了。来自 Pix el & Ton ic 的 Bran don Kel ly 甚至没有 ed[“Craft 2，和 Craft3 默认情况下，基本上都有穷人队列”](https://github.com/craftcms/cms/issues/3011#issuecomment-399521965)。

<aside>Run­ning lengthy oper­a­tions via web request rarely ends well</aside>

Web 请求实际上意味着相对较短的原子操作，比如加载网页。因此，web 服务器和 PHP 进程都有超时时间，以防止它们花费太长时间。

[![Stuck queue jobs](img/49838120a7426bf51a72513577f84739.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8I6Ilw08--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x780_crop_center-center_100_line/stuck-queue-jobs.png)

这会导致不好的事情发生:

*   队列作业堆积起来，直到有人登录到 CP
*   如果队列作业耗时太长且超时，它们可能会失败或停滞
*   如果队列作业耗尽内存，它们可能会失败或停滞
*   过长的队列作业在运行时会影响 CP 或 fron 性能

事实上，我们可以解决所有这些问题，而且不难做到！

如果您确实有因错误而失败的任务，请查看 [Zen 和 Art of Craft CMS 日志文件阅读](https://dev.to/gaijinity/zen-and-the-art-of-craft-cms-log-file-reading-3dp3-temp-slug-5047256)文章，了解如何调试它。

## 排队跑宁方案

对于 tu nate ly Yii2，并且通过扩展 Craft CMS，附带了一套强大的命令行工具，我在[探索 Craft CMS 3 唯一命令行接口(CLI)](https://dev.to/gaijinity/exploring-the-craft-cms-3-console-command-line-interface-cli-452-temp-slug-1468335) 一文中详细介绍了这些工具。

[![Shift into high gear](img/ef35c2c0bcb61ab14f1867944c033a67.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XZXFBx_W--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x675_crop_center-center_82_line/shift-into-high-gear.jpg)

这个`queue`命令让我们能够通过直接运行队列作业来将它们转换到高速运行状态。通过唯一命令行接口(CLI)运行的 PHP 代码使用的是一个单独的`php.ini`文件，而不是 web-ori ent ed `php-fpm`文件。这是我服务器上的一个例子:

```
 /etc/php/7.1/fpm/php.ini
/etc/php/7.1/cli/php.ini 
```

这一点很重要，因为基于 web 的`php-fpm`可能有合理的[内存限制](https://www.php.net/manual/en/ini.core.php#ini.memory-limit)和[最大执行时间](https://www.php.net/manual/en/info.configuration.php#ini.max-execution-time)设置，而基于 CLI 的 PHP 通常没有`memory_limit`和`max_execution_time`。

这是因为 web 进程是外部触发的，因此是不可信的，而通过 CLI 运行的东西是内部可信的。

因此，通过 PHP CLI 运行我们的队列作业给我们带来了以下好处:

*   可以在任何时候运行，而不仅仅是当有人访问 CP 时
*   不会因执行时间过长而中断
*   不太可能耗尽内存

我们使用的队列唯一命令是:

*   `queue/run` —运行队列中的所有作业，然后退出
*   `queue/listen` — Per pet 通常运行一个进程，列出添加到队列中的十个新任务并运行它们

有几种方法可以运行这些命令，在[yii 2 Work er Start control](https://www.yiiframework.com/extension/yiisoft/yii2-queue/doc/guide/2.0/en/worker)文章中讨论过，我们将在这里详细讨论它们。

## solu station # 1:异步队列插件

如果你想要一个简单的解决方案，只需要安装一个插件，看看 Oliv er Stark 的[异步队列插件。](https://github.com/ostark/craft-async-queue)从插件的描述来看:

*[Async Queue plu in]替换 Craft 的默认队列处理程序，并将队列执行移动到非阻塞后台进程。命令进程`queue/run`在你将一个任务推入队列后立即被执行。*

如果你想要一个简单的解决方案，只需要安装一个插件就可以了，那就走这条路吧。您最终将获得一个运行队列作业的更健壮的解决方案。

**就这样，大功告成！**

## 解决方案 2:伪造守护进程

如果你使用 win der ful[Lar avel Forge](https://forge.laravel.com/)来供应你的服务器(在[自由职业者应该如何做虚拟主机](https://dev.to/gaijinity/how-agencies-freelancers-should-do-web-hosting-ih4-temp-slug-5481912)的文章中有详细讨论)，Forge 为它所谓的**主机**提供了一个漂亮的 GUI 界面。

转到您的服务器，然后单击 Dae mons，您将看到一个如下所示的 GUI:

[![Forge new daemon](img/a45453e4576c87cfc437063d2b71ee6e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vOxbY7S5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x505_crop_center-center_100_line/forge-new-daemon.png)

这是我们为[开发模式添加的命令。fm](https://devmode.fm/) 网址:

```
 /usr/bin/nice -n 10 /usr/bin/php /home/forge/devmode.fm/craft queue/listen --verbose 
```

让我们来分解一下:

*   `/usr/bin/nice -n 10` —它使用 Unix [nice](https://en.wikipedia.org/wiki/Nice_(Unix)) 命令，以较低的优先级运行进程，因此不会干扰 CP 或前端
*   `/usr/bin/php /home/forge/devmode.fm/craft queue/listen --verbose` —这将启动一个 PHP CLI 进程，列出所有队列作业的清单，并运行它们

您应该换入自己的路径到`craft` CLI 可执行文件；我们的是`/home/forge/devmode.fm/craft`

我们告诉它，我们希望以用户`forge`的身份运行它，并且我们希望这些进程的`2`运行。点击**启动 Dae mon** ，你会看到:

[![Forge daemon added](img/16c34982ecdb281d43fde59760195b9d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZzBxo3xO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x246_crop_center-center_100_line/forge-daemon-added.png)

然后确保您已经为您的`live`生产环境
将[运行队列设置为](https://docs.craftcms.com/v3/config/config-settings.html#runqueueautomatically)工艺 CMS `general.php`配置设置为`false`

```
 // Live (production) environment
    'live' => [
        // Craft defined config settings
        'runQueueAutomatically' => false,
    ], 
```

这确保了 Craft 不再试图通过 web 请求运行它的队列。

**就这样，大功告成！**

下面是一个完整的选项在引擎盖下窥视如何与 Lar avel 锻造工程。如果你对血淋淋的细节不感兴趣，可以跳过它。

Forge 所谓的 Dae mons 实际上是通过[超级浏览器](http://supervisord.org/)命令运行的命令。当我们在 Forge GUI 中创建上述 dae mon 时，它会在`/etc/supervisor/conf.d` :
中创建一个文件

```
 forge@nys-production /etc/supervisor/conf.d $ ls -al
-rw-r--r-- 1 root root 293 Aug 3 14:55 daemon-157557.conf 
```

该文件的内容如下:

```
 [program:daemon-157557]
command=/usr/bin/nice -n 10 /usr/bin/php /home/forge/devmode.fm/craft queue/listen --verbose

process_name=%(program_name)s_%(process_num)02d
autostart=true
autorestart=true
user=forge
numprocs=2
redirect_stderr=true
stdout_logfile=/home/forge/.forge/daemon-157557.log 
```

事实上，这正是在[yi 2 Work er Start control](https://www.yiiframework.com/extension/yiisoft/yii2-queue/doc/guide/2.0/en/worker)文章中推荐的方法之一。万岁。

## 使用 Heroku 的解决方案 3

如果你使用 [Heroku](https://www.heroku.com/) 来满足你的主机需求，你可以使用 [Work er Dynos](https://devcenter.heroku.com/articles/background-jobs-queueing) 来运行你的`queue/listen`命令:

```
 ./craft queue/listen --verbose 
```

然后确保您已经为您的`live`生产环境
将[运行队列设置为](https://docs.craftcms.com/v3/config/config-settings.html#runqueueautomatically)工艺 CMS `general.php`配置设置为`false`

```
 // Live (production) environment
    'live' => [
        // Craft defined config settings
        'runQueueAutomatically' => false,
    ], 
```

这确保了 Craft 不再试图通过 web 请求运行它的队列。

**就这样，大功告成！**

## solu station # 4:uti Liz ing 系统

如果你正在使用阿林 ux serv er，但你没有使用 Forge 或 Heroku，或者你只是想自己配置一些东西，我们可以使用[system](https://www.linode.com/docs/quick-answers/linux-essentials/what-is-systemd/)来完成同样的事情。

是一种启动、停止和管理运行在服务器上的守护进程的方式。不管你是否知道，你已经在使用它了，比如你的 web 服务器，MySQL 等等。

以下是我为[开发模式所做的。fm](https://devmode.fm/) 使用`systemd`来运行我们的队列作业。您将需要`sudo`访问权限才能做到这一点，但对于大多数现代 VPS 主机服务来说，您自然可以获得这种能力。

首先我通过
在`/etc/systemd/system/`创建了一个名为`[devmode-queue@.service](mailto:devmode-queue@.service)`的`.service`文件

```
 forge@nys-production ~ $ sudo nano /etc/systemd/system/devmode-queue@.service 
```

下面是该文件的内容:

```
 [Unit]
Description=devMode.fm Queue Worker %I
After=network.target
After=mysql.service
Requires=mysql.service

[Service]
User=forge
Group=forge
ExecStart=/usr/bin/nice -n 10 /usr/bin/php /home/forge/devmode.fm/craft queue/listen --verbose
Restart=always

[Install]
WantedBy=multi-user.target 
```

让我们来分解一下:

*   `/usr/bin/nice -n 10` —它使用 Unix [nice](https://en.wikipedia.org/wiki/Nice_(Unix)) 命令，以较低的优先级运行进程，因此不会干扰 CP 或前端
*   `/usr/bin/php /home/forge/devmode.fm/craft queue/listen --verbose` —这将启动一个 PHP CLI 进程，列出所有队列作业的清单，并运行它们

您应该换入自己的路径到`craft` CLI 可执行文件；我们的是`/home/forge/devmode.fm/craft`

我们告诉它，我们希望以用户`forge`的身份运行它，我们希望它在失败时重启进程，并且它取决于`network`和`mysql`服务是否可用。

`.service`文件名中奇怪的`@`允许我们运行这个服务的多个实例。然后我们可以使用范围 syn tax `{1..2}`来指定我们需要多少这样的进程。

我们已经创建了文件，但是我们需要通过:
启动服务

```
 forge@nys-production ~ $ sudo systemctl start devmode-queue@{1..2} 
```

然后，就像我们的其他服务，如`nginx`和`mysqld`一样，我们希望它在我们重启服务器时自动启动:

```
 forge@nys-production ~ $ sudo systemctl enable devmode-queue@{1..2} 
```

然后确保您已经为您的`live`生产环境
将[运行队列设置为](https://docs.craftcms.com/v3/config/config-settings.html#runqueueautomatically)工艺 CMS `general.php`配置设置为`false`

```
 // Live (production) environment
    'live' => [
        // Craft defined config settings
        'runQueueAutomatically' => false,
    ], 
```

这确保了 Craft 不再试图通过 web 请求运行它的队列。

**就这样，大功告成！**

现在，您可以启动、停止、启用、禁用等。您精心设计的 CMS 队列运行起来就像您运行任何其他系统服务一样。你也可以通过 [jour nalctl](https://www.linode.com/docs/quick-answers/linux/how-to-use-journalctl/) 命令:
来监控它

```
 forge@nys-production ~ $ sudo journalctl -f -u devmode-queue@*.service
-- Logs begin at Sat 2019-08-03 13:23:17 EDT. --
Aug 03 14:06:04 nys-production nice[1364]: Processing element 42/47 - Neutrino: How I Learned to Stop Worrying and Love Webpack
Aug 03 14:06:04 nys-production nice[1364]: Processing element 43/47 - Google AMP: The Battle for the Internet's Soul?
Aug 03 14:06:04 nys-production nice[1364]: Processing element 44/47 - The Web of Future Past with John Allsopp
Aug 03 14:06:04 nys-production nice[1364]: Processing element 45/47 - Web Hosting with ArcusTech's Nevin Lyne
Aug 03 14:06:04 nys-production nice[1364]: Processing element 46/47 - Tailwind CSS utility-first CSS with Adam Wathan
Aug 03 14:06:04 nys-production nice[1364]: Processing element 47/47 - Talking Craft CMS 3 RC1!
Aug 03 14:06:04 nys-production nice[1364]: 2019-08-03 14:06:04 [1427] Generating episodes sitemap (attempt: 1) - Done (1.377 s)
Aug 04 09:25:25 nys-production nice[1364]: 2019-08-04 09:25:25 [1429] Generating calendar sitemap (attempt: 1) - Started
Aug 04 09:25:25 nys-production nice[1364]: Processing element 1/1 - Calendar of Upcoming Episodes
Aug 04 09:25:25 nys-production nice[1364]: 2019-08-04 09:25:25 [1429] Generating calendar sitemap (attempt: 1) - Done (0.174 s) 
```

上述命令将跟踪`systemd`日志，只显示来自我们的`devmode-queue`服务的消息。

你可以在[如何使用 Jour nalctl 查看和操作最新系统日志](https://www.digitalocean.com/community/tutorials/how-to-use-journalctl-to-view-and-manipulate-systemd-logs)一文中了解更多关于使用`journalctl`的信息。

## 得到廷的宠爱

根据 [php dotenv](https://github.com/vlucas/phpdotenv) 软件包作者的建议，如果您对学习如何在生产中不使用`.env`文件不感兴趣，可以跳过这一部分:

php 程序是为开发环境而设计的，一般不应该用于生产。**在生产中，应设置实际环境变量，以便不会出现过载。env 文件。**这可以通过一个自动的 mat ed 部署过程来实现，使用的工具有流浪者、厨师或 Pup pet 等，也可以通过 Pagod abox 和 Heroku 等云主机来手动设置。

<aside>You do not need to do any of this unless you do not use .env in production</aside>

此外，有些设置通过改变方式引入了环境变量。我制作了一个[dotten vy](https://github.com/nystudio107/dotenvy)包，使得生成`.env`键/值可变对成为 Apache、Nginx 和 shell 等价物变得容易。

在这种情况下，我们需要稍微修改我们运行的命令，以便在通过 CLI 运行 Craft 之前，我们在 shell 中定义我们的环境变量。

我们需要做的就是将我们的命令改为:

```
 /bin/bash -c ". /home/forge/devmode.fm/.env_cli.txt && /usr/bin/nice -n 10 /usr/bin/php /home/forge/devmode.fm/craft queue/listen --verbose" 
```

我们所做的只是 exe cut 文件(由 [Doten vy](https://github.com/nystudio107/dotenvy) 创建)包含我们对 shell 环境`export`的环境变量:

```
 forge@nys-production ~/devmode.fm (master) $ cat .env_cli.txt
# CLI (bash) .env variables
# Paste these inside your .bashrc file in your $HOME directory:
export ENVIRONMENT="live"
export SECURITY_KEY="XXXXX"
export DB_DRIVER="pgsql"
export DB_SERVER="localhost"
export DB_USER="devmode"
export DB_PASSWORD="XXXXX"
export DB_DATABASE="devmode"
export DB_SCHEMA="public"
export DB_TABLE_PREFIX=""
export DB_PORT="5432"
export SITE_URL="https://devmode.fm/"
export BASE_PATH="/home/forge/devmode.fm/web/"
export BASE_URL="https://devmode.fm/"
export REDIS_HOSTNAME="localhost"
export REDIS_PORT="6379"
export REDIS_DEFAULT_DB="0"
export REDIS_CRAFT_DB="3"
export PUBLIC_PATH="/dist/"
export DEVSERVER_PUBLIC="http://192.168.10.10:8080"
export DEVSERVER_HOST="0.0.0.0"
export DEVSERVER_POLL="1"
export DEVSERVER_PORT="8080"
export DEVSERVER_HTTPS="0" 
```

其余的与我们在上面讨论的运行`queue/listen`的命令相同，然后我们把它们都包装在`/bin/sh -c`中，这样它就作为一个可以执行的命令出现。

## 深度排队

这里有一些你可能会感兴趣的队列工作 triv ia。

[![Craft cms queue job trivia](img/b0ec870d4d6b75915a99103a6869dd54.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Q97kIph6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x675_crop_center-center_82_line/craft-cms-queue-job-trivia.jpg)

我们听说队列作业正常运行的唯一方式是您查看它的 CP。这是最真实的。

实际上，队列作业可以通过前端页面加载来运行。如果您已经将[生成的运输单被前置装载](https://docs.craftcms.com/v3/config/config-settings.html#generatetransformsbeforepageload)设置为`false`(这是默认设置)，并且有待生成的运输单需要生成。

所发生的是`Assets::getAssetUrl()`方法推送图像传输队列作业，它调用`Queue::push()` :

```
 /**
     * @inheritdoc
     */
    public function push($job)
    {
        // Capture the description so pushMessage() can access it
        if ($job instanceof JobInterface) {
            $this->_jobDescription = $job->getDescription();
        } else {
            $this->_jobDescription = null;
        }

        if (($id = parent::push($job)) === null) {
            return null;
        }

        // Have the response kick off a new queue runner if this is a site request
        if (Craft::$app->getConfig()->getGeneral()->runQueueAutomatically && !$this->_listeningForResponse) {
            $request = Craft::$app->getRequest();
            if ($request->getIsSiteRequest() && !$request->getIsAjax()) {
                Craft::$app->getResponse()->on(Response::EVENT_AFTER_PREPARE, [$this, 'handleResponse']);
                $this->_listeningForResponse = true;
            }
        }

        return $id;
    } 
```

因此，如果这是一个站点(fron tend)请求，而不是一个 AJAX 请求，Craft 将添加一个对`Queue::handleResponse()`的调用，当`Response`即将被发送回客户端时，该调用将被触发。

在这种情况下，Craft 将在 fron tend 请求中注入一些 JavaScript，通过 AJAX:
ping`queue/run`控制器

```
 /**
     * Figure out how to initiate a new worker.
     */
    public function handleResponse()
    {
        // Prevent this from getting called twice
        $response = Craft::$app->getResponse();
        $response->off(Response::EVENT_AFTER_PREPARE, [$this, 'handleResponse']);

        // Ignore if any jobs are currently reserved
        if ($this->getHasReservedJobs()) {
            return;
        }

        // Ignore if this isn't an HTML/XHTML response
        if (!in_array($response->getContentType(), ['text/html', 'application/xhtml+xml'], true)) {
            return;
        }

        // Include JS that tells the browser to fire an Ajax request to kick off a new queue runner
        // (Ajax request code adapted from http://www.quirksmode.org/js/xmlhttp.html - thanks ppk!)
        $url = Json::encode(UrlHelper::actionUrl('queue/run'));
        $js = <<<EOD
<script type="text/javascript">
/*<![CDATA[*/
(function(){
    var XMLHttpFactories = [
        function () {return new XMLHttpRequest()},
        function () {return new ActiveXObject("Msxml2.XMLHTTP")},
        function () {return new ActiveXObject("Msxml3.XMLHTTP")},
        function () {return new ActiveXObject("Microsoft.XMLHTTP")}
    ];
    var req = false;
    for (var i = 0; i < XMLHttpFactories.length; i++) {
        try {
            req = XMLHttpFactories[i]();
        }
        catch (e) {
            continue;
        }
        break;
    }
    if (!req) return;
    req.open('GET', $url, true);
    if (req.readyState == 4) return;
    req.send();
})();
/*]]>*/
</script>
EOD;

        if ($response->content === null) {
            $response->content = $js;
        } else {
            $response->content .= $js;
        }
    } 
```

虽然技术人员称这种情况可能发生在任何在投标请求过程中排队的工作中，但资产转移是我所知道的唯一一种实际发生的情况。

因此，如果你发布任何 XHR 请求给工艺控制器或 API 端点，确保你添加了标题:

```
 'X-Requested-With': 'XMLHttpRequest' 
```

Craft 查找这个头来确定请求是否是 AJAX，如果请求是 AJAX，就不会尝试将其队列运行 JavaScript 附加到请求上。

这个头文件是由 jQuery 为`$.ajax()`请求自动设置的，但是它不是由诸如 [Axios](https://www.npmjs.com/package/axios) 之类的流行库自动设置的。

## 把乒包起来

那么我用哪种方法呢？如果我正在用 Forge 构建一个 VPS(我通常都是这样)，那么我将使用**解决方案#2: Forge Dae mons** 。如果它不是一个伪造的盒子，但是我有`sudo`权限，那么我将使用**解决方案#3:应用系统**。

我在客户端站点上使用**解决方案#1:在**中的异步队列插入，而上述两种情况都不存在。Oliv er 的插件很棒，比默认的队列作业运行方式好得多，但是如果可能的话，我喜欢利用现有的系统，并且拥有对进程优先级的控制也很好。

[![There is always hope](img/cf2981d4c1a3d553c2afdc4e25063804.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uoS1rlPf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x675_crop_center-center_82_line/there-is-always-hope.jpg)

将 Craft CMS 中默认的基于 web 的队列作业系统替换为更健壮的基于 CLI 的系统将会带来好处。

您的队列作业将运行得更流畅，更少失败，对 CP/前端的影响也更小。

值了。动手吧。

快乐排队！

## 进一步阅读

如果你想获得新文章的通知，请在 Twitter 上关注[纽约时报 107](https://twitter.com/nystudio107) 。

版权所有 2020 nystudio107。由 nystudio107 设计