# 为 Craft CMS 创建自定义日志程序

> 原文：<https://dev.to/gaijinity/creating-a-custom-logger-for-craft-cms-425d>

# 为 Craft CMS 创建自定义记录器

### 内置的 Craft CMS 日志系统足以满足大多数需求，但如果您需要自定义日志，或者想要将您的日志发送到第三方服务，该怎么办？

安德鲁·韦尔奇

[![Writing a custom logger craft cms](img/789c23243163675681fd4f7a24944739.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ciT6c9FY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x675_crop_center-center_82_line/writing-a-custom-logger-craft-cms.jpg)

Craft CMS 测井系统(基于 [Yii 测井](https://www.yiiframework.com/doc/guide/2.0/en/runtime-logging)系统)通常足以满足大多数需求。事实上，许多人甚至不去想它，直到出了问题。

但是，如果您需要改变内置的日志行为，该怎么办呢？或者您需要实现某种自定义日志行为？或者您想将手工 CMS 日志发送到一个处理日志聚合的 SaaS，如 [Paper Trail](https://papertrailapp.com/) 或 [Sen try](https://sentry.io) ？

在本文中，我们将学习如何处理上述三种情况。我们不会讨论如何读取标准的 CMS 日志文件；为此，请查看 Zen 和 CMS 日志文件艺术阅读文章。

<aside>Cus­tom log­ging in Craft CMS is pret­ty easy to do, thanks to Yii</aside>

要创建自定义日志，我们需要使用一些配置或代码，这取决于我们要做什么。非常感谢你让这个棘手的问题变得简单了，所以我们走吧！

Craft CMS 的 web 应用程序高度依赖于 [Yii 配置](https://www.yiiframework.com/doc/guide/2.0/en/concept-configurations)作为动态配置自身的一种方式。它在引擎盖下使用[依赖于注射容器](https://www.yiiframework.com/doc/guide/2.0/en/concept-di-container)来容纳它。

虽然您也不必完全理解本文，但它将有助于您全面了解正在发生的事情。

## 定制现有的记录器

您可能需要做的第一件事是定制 Craft CMS 默认使用的现有基于文件的日志。

[![Logging lumberjack 01](img/9f5b7973569a2b9b76f333732461f0da.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ae6TwM2b--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x675_crop_center-center_82_line/logging-lumberjack-01.jpg)

下面是一个示例，说明只需一个客户配置就可以更改哪些属性:

*   `includeUserIp` — *默认:* `false` —默认日志前缀中是否包含用户 IP
*   `logFile` — *默认:* `@storage/logs/web.log` —日志文件路径或路径别名
*   `enableRotation` — *默认:* `true` —日志文件达到一定程度时是否应该轮转`maxFileSize`
*   `maxFileSize` — *默认:* `10240` —最大日志文件大小，单位为千字节
*   `maxLogFiles` — *默认:* `5` —用于轮转的日志文件数量
*   `levels` — *默认* : `0`(全部)`devMode`开，`3` ( `Logger::LEVEL_ERROR | Logger::LEVEL_WARNING`)关`devMode`—应该记录在日志中的日志级别的[位掩码](https://github.com/yiisoft/yii2/blob/master/framework/log/Logger.php#L46)

你也可以定制一些其他的设置，但是它们通常不是很有趣。详见[FileTarget.php](https://github.com/yiisoft/yii2/blob/master/framework/log/FileTarget.php#L26)。

那么我们如何做到这一点呢？我们可以通过添加一个`log`组件:
来配置通过`config/app.php`文件制作 CMS 的 web app

```
 <?php

return [
    'components' => [
        'log' => function() {
            $config = craft\helpers\App::logConfig();
            if ($config) {
                $config['targets'][0]['includeUserIp'] = false;
                $config['targets'][0]['logFile'] = '@storage/logs/web.log';
                $config['targets'][0]['enableRotation'] = true;
                $config['targets'][0]['maxFileSize'] = 10240;
                $config['targets'][0]['maxLogFiles'] = 5;
                return Craft::createObject($config);
            }
            return null;
        },
    ],
]; 
```

这里有一点要解开，但没有什么我们不能处理的！我们将`log`组件设置为一个[匿名函数](https://www.php.net/manual/en/functions.anonymous.php)，该函数从内置的 Craft helper 方法`craft\helpers\App::logConfig()`获得一个基`$config`。

我认为 Pix el & Ton ic 编写了一个 helper 方法，因为 Craft CMS 使用的默认日志配置有一些内容。让我们来看看:

```
 /**
     * Returns the `log` component config.
     *
     * @return array|null
     */
    public static function logConfig()
    {
        // Only log console requests and web requests that aren't getAuthTimeout requests
        $isConsoleRequest = Craft::$app->getRequest()->getIsConsoleRequest();
        if (!$isConsoleRequest && !Craft::$app->getUser()->enableSession) {
            return null;
        }

        $generalConfig = Craft::$app->getConfig()->getGeneral();

        $target = [
            'class' => FileTarget::class,
            'fileMode' => $generalConfig->defaultFileMode,
            'dirMode' => $generalConfig->defaultDirMode,
            'includeUserIp' => $generalConfig->storeUserIps,
            'except' => [
                PhpMessageSource::class . ':*',
            ],
        ];

        if ($isConsoleRequest) {
            $target['logFile'] = '@storage/logs/console.log';
        } else {
            $target['logFile'] = '@storage/logs/web.log';

            // Only log errors and warnings, unless Craft is running in Dev Mode or it's being installed/updated
            if (!YII_DEBUG && Craft::$app->getIsInstalled() && !Craft::$app->getUpdates()->getIsCraftDbMigrationNeeded()) {
                $target['levels'] = Logger::LEVEL_ERROR | Logger::LEVEL_WARNING;
            }
        }

        return [
            'class' => Dispatcher::class,
            'targets' => [
                $target,
            ]
        ];
    } 
```

我们不需要研究它在这里做的每一件事，但至少让我们看看如果`devMode`开启(因此`levels`不受限制):
这个`$config`类型调用返回时是什么样子

```
 [
    'class' => 'yii\\log\\Dispatcher'
    'targets' => [
        0 => [
            'class' => 'craft\\log\\FileTarget'
            'fileMode' => null
            'dirMode' => 509
            'includeUserIp' => false
            'except' => [
                0 => 'yii\\i18n\\PhpMessageSource:*'
            ]
            'logFile' => '@storage/logs/web.log'
        ]
    ]
] 
```

从这个角度来看，我们为什么这么做就更有意义了:

```
 $config['targets'][0]['enableRotation'] = true; 
```

日志配置具有几乎所有日志都具有的`yii\log\Dispatcher`的`class`，因为这个核心组件处理从日志到任意数量的注册目标的分发消息。

这是我们在配置中设置的另一个属性，一个数组`targets`。目标数组有一个配置，用于扩展抽象类`yii\log\Target`的任意数量的类。

`targets`数组中的第一个(`0`元素是 Craft CMS 的默认文件 tar get，因此我们可以在这里为该日志 tar get 设置配置设置，在本例中为`enableRotation`。

让我们看一个实际的例子。对于通过命令行运行的 [Craft con sole 请求](https://dev.to/gaijinity/exploring-the-craft-cms-3-console-command-line-interface-cli-452-temp-slug-1468335)，默认情况下[开发模式是启用的](https://github.com/craftcms/cms/blob/develop/bootstrap/bootstrap.php#L170-L184)，但是我们可以很容易地解决这个问题:

```
 <?php

use craft\helpers\ArrayHelper;
use yii\log\Logger;

return [
    'components' => [
        'log' => function() {
            $config = craft\helpers\App::logConfig();
            if ($config) {
                $generalConfig = Craft::$app->getConfig()->getGeneral();
                $devMode = ArrayHelper::getValue($generalConfig, 'devMode', false);
                // Only log errors and warnings, unless Craft is running in Dev Mode or it's being installed/updated
                if (!$devMode && Craft::$app->getIsInstalled() && !Craft::$app->getUpdates()->getIsCraftDbMigrationNeeded()) {
                    $config['targets'][0]['levels'] = Logger::LEVEL_ERROR | Logger::LEVEL_WARNING;
                }

                return Craft::createObject($config);
            }
            return null;
        },
    ],
]; 
```

这将有效地使控制台请求像 web 请求一样工作，因为只有当`devMode`关闭时，它才会记录`Logger::LEVEL_ERROR | Logger::LEVEL_WARNING`日志级别。

## 自定义插件/模块登录行为

定制日志行为的另一个用例是，如果您有一个插件或模块，您希望将它的所有日志消息发送到一个单独的速率文件中。

[![Logging lumberhack 03](img/fec41ee68c470b056615d525799680a0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_fadks1U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_992x558_crop_center-center_82_line/logging-lumberhack-03.jpg)

也许为了调试目的，我们希望能够只查看我们的插件或模块记录的消息，而不查看其他日志消息。

我们实际上可以很容易地做到这一点，只需向我们的插件或模块的`init()`方法:
添加一点代码

```
 public function init() {
    // Create a new file target
    $fileTarget = new \craft\log\FileTarget([
        'logFile' => '@storage/logs/retour.log',
        'categories' => ['nystudio107\retour\*']
    ]);
    // Add the new target file target to the dispatcher
    Craft::getLogger()->dispatcher->targets[] = $fileTarget;
} 
```

这创建了一个新的`craft\log\FileTarget`初始化，自定义`logFile`设置为`@storage/logs/retour.log`。它通过`Craft::getLogger()`获取当前日志，并在`$fileTarget`处将新的 ly cre 添加到 dispatcher 中的现有数组`targets`中。

我们将`categories`数组设置为一个条目:`nystudio107\retour*`。这意味着只有当目录设置为以【T2 是通配符】开头的任何内容时，该日志目标才会被发送日志消息。

<aside>This allows us to lever­age the exist­ing log­ging sys­tem very effectively.</aside>

这将记录来自 Retour 插件名称空间`nystudio107\retour*`的任何消息，因为当我们记录时，我们做`Craft::error('message', **METHOD** );`

`**METHOD**` [PHP 磁常数](https://www.php.net/manual/en/language.constants.predefined.php)输出电流`fully\qualified\class::method`。

这样做的好处是，除了将插件的消息记录到单独的日志文件之外，它们还会访问主日志文件。我们刚刚添加了一个额外的日志目标。

这很重要，因为我们仍然可以使用 Yii 调试工具栏来筛选所有的日志，就像在文章[用 Craft CMS 3 的调试工具栏填充你的网站](https://dev.to/gaijinity/profiling-your-website-with-craft-cms-3-8217-s-debug-toolbar-1jig-temp-slug-5108979)中描述的那样。

**注意:**这个方法是对[罗宾这里](https://craftcms.stackexchange.com/questions/25427/craft-3-plugins-logging-in-a-separate-log-file)所用方法的改进；这里还有一个由[凯西火腿制作的变种](https://stackoverflow.com/questions/24507489/how-to-maintain-different-log-files-for-different-purposes-in-yii2)。

## 一个完整的自定义记录器

假设您想要一个完整的定制日志程序，可能会将手工 CMS 日志发送到一个处理日志聚合的 SaaS，例如[文件跟踪](https://papertrailapp.com/)或 [Sen try](https://sentry.io/) 或类似的。

[![Logging lumberjack 03](img/7d0ee55f711029ad5d42a9877337b386.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8jYmhpzH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x675_crop_center-center_82_line/logging-lumberjack-03.jpg)

在这种情况下，我们既需要一些自定义配置，也需要自定义代码。让我们从配置开始；我们将再次需要在我们的`config/app.php` :
中放置一个客户`log`组件

```
 <?php

return [
    'components' => [
        'log' => [
            'class' => 'yii\\log\\Dispatcher'
            'targets' => [
                [
                    'class' => 'modules\sitemodule\log\CustomTarget',
                    'logFile' => '@storage/logs/custom.log',
                    'levels' => ['error', 'warning'],
                    'exportInterval' => 100,
                ],
            ],
        ],
    ],
]; 
```

在这种情况下，我们通过配置数组将`log`组件完全替换为我们自己的组件。tar get 的`class`指向我们在`modules\sitemodule\log\CustomTarget`的站点模块名称空间中的一个定制日志 tar get。

如果您不熟悉定制模块，请查看[增强 Craft CMS 3 网站上的一篇定制模块](https://dev.to/gaijinity/enhancing-a-craft-cms-3-website-with-a-custom-module-33g1-temp-slug-8101978)文章。

我们在这里还配置了一些其他合适的关系:

*   `logFile` —我们正在登录的文件(见下文)
*   `levels` —我们感兴趣的日志级别，在这里只是`error` & `warning`
*   `exportInterval` —在导出之前应该接收多少条消息

这里我们还可以配置其他适当的关系；更多信息见[Target.php](https://github.com/yiisoft/yii2/blob/master/framework/log/Target.php)。

如果我们无论如何都要把我们的日志消息发送到云中某个地方的 SaaS，你可能会明白我们为什么在这里指定一个`logFile`。

原因是我们还需要一个本地日志，以防我们的日志聚合服务出现问题，或者我们只是希望能够完全脱机调试和工作。

为此，我们的`CustomTarget`类扩展了内置的`FileTarget`类，因此我们可以免费获得它所有的文件日志记录功能，同时添加我们自己的自定义位来将数据传输到我们的 API:

```
 <?php
/**
 * Site module for Craft CMS 3.x
 *
 * An example module for Craft CMS 3 that lets you enhance your websites with a custom site module
 *
 * @link https://nystudio107.com/
 * @copyright Copyright (c) 2019 nystudio107
 */

namespace modules\sitemodule\log;

use Craft;
use craft\helpers\Json;
use craft\log\FileTarget;

/**
 * @author nystudio107
 * @package SiteModule
 * @since 1.0.0
 */
class CustomTarget extends FileTarget
{
    // Constants
    // =========================================================================

    const API_URL = 'https://api.example.com';
    const API_KEY = 'password';

    // Public Methods
    // =========================================================================

    /**
     * @inheritDoc
     */
    public function export()
    {
        // Let the parent FileTarget export the messages to a file
        parent::export();
        // Convert the messages into a JSON-encoded array of formatted log messages
        $messages = Json::encode(array_map([$this, 'formatMessage'], $this->messages));
        // Create a guzzle client, and send our payload to the API
        $client = Craft::createGuzzleClient();
        try {
            $client->post(self::API_URL, [
                'auth' => [
                    self::API_KEY,
                    '',
                ],
                'form_params' => [
                    'messages' => $messages,
                ],
            ]);
        } catch (\Exception $e) {
        }
    }
} 
```

让我们来分析一下我们正在做的事情。我们只忽略了一个方法`export()`，当需要将消息导出到外部目的地时，日志记录器在`exportInterval`调用这个方法。

首先，我们调用`parent::export()`,让我们的`FileTarget` par ent 类将日志消息导出到一个文件中。

然后我们调用 par ent `Target`类的`formatMessage`方法作为一个 [PHP 可调用的](https://www.php.net/manual/en/language.types.callable.php)，我们将它传递给 [array_ map](https://www.php.net/manual/en/function.array-map.php) 函数，该函数返回一个由我们随后 JSON 编码的格式日志行组成的数组。

最后，我们创建了一个 [Guz zle 客户端](http://docs.guzzlephp.org/en/stable/),并将我们的 JSON 编码的消息和我们的`auth`一起发送给一个简单的 API。

虽然您用来与实际 SaaS 进行尼姆通信的代码会因 API 的不同而略有不同，但方法是相同的。

如你所见，我们没有写那么多代码来实现它。

## 独白作为记录者

如果我们正在定制 Craft CMS 日志记录器，一个非常典型的用例是我们只是将日志数据发送到某个第三方日志服务，如 [Paper Trail](https://papertrailapp.com/) 或 [Sen try](https://sentry.io/) 。我们在上一节中看到，我们可以通过编写自己的自定义记录器来实现这一点。

但是我们也可以通过调用一个名为 [Monolog](https://github.com/Seldaek/monolog) 的 PHP 库来实现。Monolog 是一种元日志程序，它可以将你的日志发送到文件、袜子、收件箱、数据库和各种网络服务中。

非常感谢，在 Craft CMS 中，这很容易设置成一个日志，只需要配置！

在我们的项目中安装独白:

```
 composer require "monolog/monolog" 
```

然后，我们还需要在我们的项目
中安装 [Yii 2 PSR 日志焦油 get](https://github.com/samdark/yii2-psr-log-target)

```
 composer require "samdark/yii2-psr-log-target" 
```

然后我们只需要在我们的`config/app.php`文件中添加一些配置来告诉它使用独白，并配置独白将日志发送到我们想要的地方:

```
 <?php

use Monolog\Logger;
use Monolog\Handler\StreamHandler;

return [
    'components' => [
        'log' => [
            'targets' => [
                [
                    'class' => \samdark\log\PsrTarget::class,
                    'except' => ['yii\web\HttpException:40*'],
                    'logVars' => [],
                    'logger' => (new Logger('app'))
                        ->pushHandler(new StreamHandler('php://stderr', \Monolog\Logger::WARNING))
                        ->pushHandler(new StreamHandler('php://stdout', \Monolog\Logger::DEBUG)),
                    'addTimestampToContext' => true,
                ]
            ],
        ]
    ],
]; 
```

这只是建立了一个 han dler，它记录到标准的 php IO 流(然后可能会到其他地方)，但是 Monolog 支持许多第三方包(T2)。

此外，它是如此流行，以至于有明确列出的服务配置，如[文件跟踪](https://help.papertrailapp.com/kb/configuration/configuring-centralized-logging-from-php-apps/#monolog)和[发送尝试](https://github.com/getsentry/sentry-docs/issues/1104)。

## 注销

我们只是简单介绍了您可以利用定制日志做些什么。例如，你可以让它正常地记录到一个文件中，但是如果有一个错误日志级别，你可以让它通过电子邮件发送给某个重要的人。

[![Logging lumberjack 06](img/638888b74d42a9a1916142daad0b6934.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--h_n4_sEd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x675_crop_center-center_82_line/logging-lumberjack-06.jpg)

希望本文能让您对使用定制日志记录的方法有所了解。

<aside>Embrace & extend, don’t rein­vent the wheel</aside>

Yii 内置了一些有趣的日志功能。杠杆化他们所做的工作，并把你的客户代码放在上面。

Hap py 日志！

## 进一步阅读

如果你想获得新文章的通知，请在 Twitter 上关注[纽约时报 107](https://twitter.com/nystudio107) 。

版权所有 2020 nystudio107。由 nystudio107 设计