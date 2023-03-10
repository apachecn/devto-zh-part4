# 使用事件管理器登录 Zend Framework

> 原文：<https://dev.to/ethanbray/logging-in-zend-framework-using-the-event-manager-2a4e>

去年，我在更新我们的客户端控制面板。客户端控制面板是一个 web 应用程序，允许我们的用户深入了解他们与我们合作的各种服务和活动。该项目的目的是使用 Zend Framework 3 组件创建一个全新的控制面板，以及更新到最新的 PHP 版本和使用前端的材料设计组件。

在我们以前版本的控制面板中，用于日志记录的基础设施非常少。日志的使用是不一致的，并且使用了各种不同的日志库，这取决于哪个开发人员在站点的那个部分工作。

经过一番研究，我们将范围缩小到三个潜在选项:

1.  为每个潜在的日志级别使用一个带有静态方法的`Logger`类。我们已经在几个较小的应用程序中使用了这种方法，但是觉得它不适合像这样的大型项目。虽然这种方法易于使用和创建，但我们不想让数百个无法模仿的静态调用污染我们的代码库。

2.  将实现`LoggerInterface`的 logger 实例注入到我们想要记录信息的任何
    类中。当时，这似乎给每个对象的构造增加了不必要的开销。

3.  使用 Zend [事件管理器](https://github.com/zendframework/zend-eventmanager)。发出日志事件(`LogEvent`)并将一个`LogListener`附加到事件管理器。然后，`LogListener`使用`LoggerInterface`的实例记录事件信息。

第三种方法似乎易于实现和理解，所以我们决定在我们的应用程序中使用它。这篇文章将介绍我们是如何实现这一点的，并且回顾这种方法，因为我们已经在生产中使用了一年多的时间。

## 创建日志事件

这是我们下面的`LogEvent`。该事件将由希望记录信息的类发出，然后由连接到我们的事件管理器的侦听器捕获。我们定义了映射到来自 [PSR-3](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-3-logger-interface.md) 的日志级别的公共常量。我们可以直接使用 PSR-3 级别，但是我们想在我们的`LogEvent`和我们使用的日志库之间提供一个抽象层。这一步是可选的，因为大多数人将使用 PSR-3 兼容的记录器。

```
declare(strict_types=1);

namespace Application\Event;

use Psr\Log\LogLevel;
use Zend\EventManager\Event;

class LogEvent extends Event
{
    public const DEBUG     = LogLevel::DEBUG;
    public const INFO      = LogLevel::INFO;
    public const NOTICE    = LogLevel::NOTICE;
    public const WARNING   = LogLevel::WARNING;
    public const ERROR     = LogLevel::ERROR;
    public const CRITICAL  = LogLevel::CRITICAL;
    public const ALERT     = LogLevel::ALERT;
    public const EMERGENCY = LogLevel::EMERGENCY;
} 
```

## 创建并注册监听器

既然我们已经有了事件，我们需要创建一个监听器并向事件管理器注册它。侦听器被附加到事件管理器，事件管理器负责对应用程序中发出的事件做出反应。

我们的侦听器实现了`ListenerAggregateInterface`，它需要两个方法:`attach`用于附加一个或多个侦听器，`detach`用于移除这些侦听器。我们使用`ListenerAggregateTrait`来实现`detach`方法。

```
declare(strict_types=1);

namespace Application\Listener;

use Application\Event\LogEvent;
use Application\Model\LoggerAwareInterface as LoggerAware;
use Psr\Log\LoggerInterface;
use Zend\EventManager\EventInterface;
use Zend\EventManager\EventManagerInterface;
use Zend\EventManager\ListenerAggregateInterface;
use Zend\EventManager\ListenerAggregateTrait;

class LogListener implements ListenerAggregateInterface
{
    use ListenerAggregateTrait;

    /**
     * @var LoggerInterface
     */
    private $logger;

    /**
     * @param LoggerInterface $logger
     */
    public function __construct(LoggerInterface $logger)
    {
        $this->logger = $logger;
    }

    /**
     * @inheritdoc
     */
    public function attach(EventManagerInterface $events, $priority = 1)
    {
        $sharedManager = $events->getSharedManager();
        $this->listeners[] = $sharedManager->attach(LoggerAware::class, LogEvent::DEBUG, [$this, 'addDebug'], $priority);
        $this->listeners[] = $sharedManager->attach(LoggerAware::class, LogEvent::INFO, [$this, 'addInfo'], $priority);
        $this->listeners[] = $sharedManager->attach(LoggerAware::class, LogEvent::NOTICE, [$this, 'addNotice'], $priority);
        $this->listeners[] = $sharedManager->attach(LoggerAware::class, LogEvent::WARNING, [$this, 'addWarning'], $priority);
        $this->listeners[] = $sharedManager->attach(LoggerAware::class, LogEvent::ERROR, [$this, 'addError'], $priority);
        $this->listeners[] = $sharedManager->attach(LoggerAware::class, LogEvent::CRITICAL, [$this, 'addCritical'], $priority);
        $this->listeners[] = $sharedManager->attach(LoggerAware::class, LogEvent::ALERT, [$this, 'addAlert'], $priority);
        $this->listeners[] = $sharedManager->attach(LoggerAware::class, LogEvent::EMERGENCY, [$this, 'addEmergency'], $priority);
    }

    private function addRecord(string $loggerLevel, EventInterface $event)
    {
        $logMessage   = $event->getTarget();
        $logContext   = $event->getParams();

        $this->logger->log($loggerLevel, $logMessage, $logContext);
    }

    public function addDebug(EventInterface $event)
    {
        return $this->addRecord(LogEvent::DEBUG, $event);
    }

    public function addInfo(EventInterface $event)
    {
        return $this->addRecord(LogEvent::INFO, $event);
    }

    // Methods must be added for each log level listed in the LogEvent
} 
```

我们特别使用了`SharedEventManager`，因为当我们想要在还没有组成`EventManager`的类的实例的情况下附加侦听器时，可以使用它。在我们的例子中，这将是任何希望发射`LogEvent`的职业。可以想象，将`LogListener`附加到每一个发出事件的类上是不实用的。

我们附加了一个监听器，并为我们的`LogEvent`中定义的每个日志级别定义了一个方法。为了可读性，上面的例子中省略了一些方法。这使得我们可以根据发出类指定的常量来记录不同级别的事件。

例如:

```
// This event will be logged as INFO due to the constant specified
$this->getEventManager()->trigger(LogEvent::INFO, 'Example log message', ['context']);

// Whereas this event will be logged as CRITICAL
$this->getEventManager()->trigger(LogEvent::CRITICAL, 'All connections down', ['context']); 
```

## 伐木工人界面

因为我们正在使用`SharedEventManager`，所以在附加侦听器时，我们需要提供一个标识符。标识符用于标识可能发出`LogEvent`的类，因此应该被监听。我们使用别名为`LoggerAware`的`LoggerAwareInterface`作为监听器的标识符。它扩展了`EventManagerAwareInterface`，因为为了`trigger`和`LogEvent`，发射类必须能够访问事件管理器的一个实例。`EventManagerAwareInterface`需要实现两个方法:`setEventManager`和`getEventManager`。

通过 Zend 服务管理器检索的任何实现了
`EventManagerAwareInterface`的类都将自动设置事件管理器。这使得发出事件变得非常容易，但确实有某种“神奇”的性质，对于以前没有使用过事件管理器的开发人员来说，这可能是不清楚的。

```
declare(strict_types=1);

namespace Application\Model;

use Zend\EventManager\EventManagerAwareInterface;

interface LoggerAwareInterface extends EventManagerAwareInterface
{

} 
```

例如，类`Foo`实现了`LoggerAwareInterface`，并且有一个方法`bar`。当调用`bar`方法时，会在事件管理器中发出一个`LogEvent`，并由连接到`LogEvent`的任何侦听器处理。我们已经自己实现了`setEventManager`和`getEventManager`方法。在`setEventManager`方法中，我们使用类名以及它实现的任何接口作为事件的标识符。您会记得之前我们使用了`LoggerAwareInterface`作为我们的标识符。

```
class Foo implements LoggerAwareInterface {
    private $events;

    public function bar(): void
    {
        $this->getEventManager()->trigger(LogEvent::INFO, 'Example log message', ['context']);
    }

    /**
     * @inheritDoc
     */
    public function setEventManager(EventManagerInterface $events)
    {
       $className = get_class($this);

       $nsPos = strpos($className, '\\') ?: 0;
       $events->setIdentifiers(array_merge(
           [
               __CLASS__,
               $className,
               substr($className, 0, $nsPos),
           ],
           array_values(class_implements($className))
       ));

       $this->events = $events;

       return $this;
    }

    /**
     * @inheritDoc
     */
    public function getEventManager()
    {
       if (!$this->events) {
           $this->setEventManager(new EventManager());
       }

       return $this->events;
    }
} 
```

## EventManagerAwareTrait

在我们希望登录的每个类中复制`setEventManager`和`getEventManager`将是一场维护的噩梦，同时也增加了“视觉债务”。我们将这些方法分成了一个`EventManagerAwareTrait`,可以在任何希望发出事件的类中使用。

```
declare(strict_types=1);

namespace Application\Model;

use Zend\EventManager\EventManager;
use Zend\EventManager\EventManagerInterface;

trait EventManagerAwareTrait
{
    private $events;

    /**
     * Set the event manager instance used by this context
     *
     * @param  EventManagerInterface $events
     * @return $this
     */
    public function setEventManager(EventManagerInterface $events)
    {
        ...
    }

    /**
     * Retrieve the event manager
     *
     * Lazy-loads an EventManager instance if none registered.
     *
     * @return EventManagerInterface
     */
    public function getEventManager()
    {
        ...
    }
} 
```

这将我们的示例简化为:

```
use Application\Model\EventManagerAwareTrait;

class Foo implements LoggerAwareInterface {
    use EventManagerAwareTrait;

    public function bar(): void
    {
        $this->getEventManager()->trigger(LogEvent::INFO, 'Example log message', ['context']);
    }
} 
```

需要注意的一点是，当从 Zend 控制器发出事件时，您不需要使用`EventManagerAwareTrait`，因为它已经实现了`setEventManager`和`getEventManager`。

## 在应用程序引导上附加监听器

快到了。在我们开始记录之前，还有一件事要做。我们需要确保我们的`LogListener`在应用程序的引导阶段连接到事件管理器。我们可以在`Application`模块的`Module.php`中这样做。

```
declare(strict_types=1);

namespace Application;

use Application\Listener\LogListener;
use Zend\EventManager\EventInterface;
use Zend\ModuleManager\Feature\BootstrapListenerInterface;
use Zend\ModuleManager\Feature\ConfigProviderInterface;

class Module implements ConfigProviderInterface, BootstrapListenerInterface
{
    /**
     * @inheritdoc
     */
    public function getConfig()
    {
        return include __DIR__ . '/../config/module.config.php';
    }

    /**
     * @inheritdoc
     */
    public function onBootstrap(EventInterface $e)
    {
        $serviceManager     = $e->getApplication()->getServiceManager();
        $eventManager       = $e->getApplication()->getEventManager();
        $sharedEventManager = $eventManager->getSharedManager();

        // Logging Listener
        $logListener = $serviceManager->get(LogListener::class);
        $logListener->attach($eventManager);
    }
} 
```

太好了！现在，我们可以通过简单地实现`LoggerAwareInterface`并使用`EventManagerAwareTrait`来轻松地从任何类中触发`LogEvent`。

## 基于事件的日志记录:1 年后

新的控制面板是一个成功，对开发者和我们的用户来说，都是一个巨大的进步。日志工作得很好，让我们对应用程序有了更好的了解，同时为开发人员提供了一致的体验。

然而，这种方法将我们的应用程序与 Zend 的事件管理器组件紧密耦合在一起。如果我们要重构我们的应用程序来使用一个不同的框架，由于耦合的原因，这将花费更长的时间。这个问题不会像我在本文开头列出的其他实现那样普遍。

在过去的一年里，我们也欢迎了一些新的开发人员加入我们的团队。这些开发人员都是初级职位，以前没有像我们的控制面板这样的应用经验。事件管理系统具有大量的“神奇”行为，这些行为对于新开发人员来说是不清楚的，尤其是那些不熟悉事件驱动架构的开发人员。我认为团队中的每个人都可以立即调试像日志这样重要的东西是很重要的，不管技能水平如何。

如果我要重新开始这个项目，我会将一个`LoggerInterface`实例传递给任何需要它的类。这种方法可能更加“手动”,但是更加明确，不会将我们束缚在任何特定的框架上。如果您已经在一个事件驱动的
系统中工作，您可能会认为与事件管理器组件的耦合不是问题。这是关于在工作中使用正确的工具。

如果你对本文有任何问题或反馈，你可以发电子邮件给我，地址是[ethan@messagecloud.com](mailto:ethan@messagecloud.com)。