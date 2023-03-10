# Swoft 2.0.3 重大更新，推出优雅的微服务治理

> 原文：<https://dev.to/stelin/swoft-2-0-3-major-update-launching-elegant-microservices-governance-3hfk>

[![](img/158e149cedc4b0a17faea04cfdafd216.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mrzIAZ7m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bw76hz9cgelzhsk96hh5.png)

## 什么是 Swoft？

Swoft 是一个基于 T2 扩展的 PHP 微服务协同程序框架。像 Go 一样，Swoft 有一个内置的协程 web 服务器和一个公共的协程客户端，驻留在内存中，独立于传统的 PHP-FPM。还有类似 Go 的语言操作，类似 Spring 的云框架灵活的注解，强大的全局依赖注入容器，全面的服务治理，灵活强大的 AOP，标准的 PSR 规范实现等等。

Swoft 通过三年的积累和方向探索，将 Swoft 打造成 PHP 界的春天云，是 PHP 高性能框架和微服务管理的不二之选。

## 更新

去除

*   移除`request->json()`方法( [c9e8f04](https://github.com/swoft-cloud/swoft-component/pull/455/commits/c9e8f048217670fd975e70d4eee1d8d3b2fccd7e) )

提高

*   添加接口依赖注入( [6169f84](https://github.com/swoft-cloud/swoft-component/pull/442/commits/6169f84ffd32622c4d3bb21f6ab3c69ef04746e2)
*   文件上传保存后添加`getFile`获取信息( [fe7e3a6](https://github.com/swoft-cloud/swoft-component/commit/fe7e3a69f215a92af83c5837d32b72a5c181383a) )
*   添加`restart()`重新启动服务( [2ffec37](https://github.com/swoft-cloud/swoft-component/pull/453/commits/2ffec37568618cc49c038f96a901cbb339769623) )
*   添加 call 1.x RPC 服务支持( [30d73c3](https://github.com/swoft-cloud/swoft-component/pull/455/commits/30d73c3bcef6294d5985ce5af316e7aa6e86c0e5) )
*   添加 AOP 类名匹配支持正则表达式( [bc5e479](https://github.com/swoft-cloud/swoft-component/pull/455/commits/bc5e47951d1426e6944b934e6c8eb5db5657f8a4) )
*   添加 RPC 服务器/Http 服务器中间件名称空间`use`错误提示( [b1cec04](https://github.com/swoft-cloud/swoft-component/pull/455/commits/b1cec041ea402fe1c837d6755eff6efbfcef3681)
*   添加带有`unfields` ( [b1bf44f](https://github.com/swoft-cloud/swoft-component/pull/459/commits/b1bf44f62ae096674f2c413e9630964782b14d3f) )的验证器排除属性字段
*   添加自动写入时间戳( [dc58011](https://github.com/swoft-cloud/swoft-component/pull/457/commits/dc58011cfa90996e6e319365fc738c1cd386f08d) )
*   添加模型动作事件( [dc58011](https://github.com/swoft-cloud/swoft-component/pull/457/commits/dc58011cfa90996e6e319365fc738c1cd386f08d)
*   添加数据库迁移脚本( [26bb464](https://github.com/swoft-cloud/swoft-ext/commit/26bb464ba61e43b6bbae9ba191a8e69525ad7dc5) )
*   添加实体自动与 json 和数组进行交互( [dc58011](https://github.com/swoft-cloud/swoft-component/pull/457/commits/dc58011cfa90996e6e319365fc738c1cd386f08d) )
*   添加型号批量更新方法`batchUpdateByIds` ( [dc58011](https://github.com/swoft-cloud/swoft-component/pull/457/commits/dc58011cfa90996e6e319365fc738c1cd386f08d) )

固定:

*   修复 Cookie 设置时的一些问题，添加一些与 withCookie( [b05afbb01](https://github.com/swoft-cloud/swoft-component/pull/443/commits/b05afbb011457d217bb3a40c6a3639c28b0eef18) )相关的方法
*   修复了当控制台使用协程模式运行命令时没有捕获处理错误。( [8a5418bf](https://github.com/swoft-cloud/swoft-component/pull/443/commits/8a5418bf25a4d4797d99281bf7db8881b7ca43ed) )
*   修复重启 websocket 服务器命令没有先停止旧服务器的问题( [db2d935](https://github.com/swoft-cloud/swoft-component/pull/443/commits/db2d935542c715bbe1ed4b3ed822b2d946317b6f) )
*   修复任务返回值为`null` ( [a69347c](https://github.com/swoft-cloud/swoft-component/pull/455/commits/a69347c8ce486102e23bb82d70cfef63573a94eb) )
*   修复 RPC Server only 类中间件无法使用( [204bc7f](https://github.com/swoft-cloud/swoft-component/pull/455/commits/204bc7f4f23a26f75534ac808e2f9eb05a0118a2) )
*   修复 RPC 服务器返回值为`null` ( [4d091be](https://github.com/swoft-cloud/swoft-component/pull/455/commits/4d091bef0ece773bcd382a750de01eef830f2949) )
*   修复记录器和阻塞日志级别不能被覆盖且无效( [8eb8aba](https://github.com/swoft-cloud/swoft-component/pull/459/commits/8eb8aba2313a45191318b52a3698135d92cb3e71) )
*   模型中的固定属性不支持自定义表达式( [dc58011](https://github.com/swoft-cloud/swoft-component/pull/457/commits/dc58011cfa90996e6e319365fc738c1cd386f08d) )

更新:

*   验证器优化，支持自定义验证规则( [d959a4f](https://github.com/swoft-cloud/swoft-component/pull/442/commits/d959a4f5facfc24070fbde547f0557213e5184ee) )
*   将错误处理管理类`ErrorHanlders`重命名为`ErrorManager` ( [f3a8f04b](https://github.com/swoft-cloud/swoft-component/pull/443/commits/f3a8f04ba6d83874b772a419fe29e5449b3dcea9) )
*   控制台组件的异常处理改为由错误组件提供的统一处理风格。( [4f47204](https://github.com/swoft-cloud/swoft-component/pull/443/commits/4f472045e3d80165145907331386f46a2912b2e3) )
*   控制台组件允许设置禁用命令组( [c5a0269](https://github.com/swoft-cloud/swoft-component/pull/454/commits/c5a0269f26ae6b9739401393a48fd3fd91280bbb) )
*   在默认的错误处理中，允许设置错误捕获级别。默认等级为`E_ALL | E_STRICT` ( [afff9029](https://github.com/swoft-cloud/swoft-component/pull/443/commits/afff90296d80085acd30b951441774049d0ca2e7) )
*   优化 ws 服务器启动时，同时启用 http 处理功能，信息面板增加提示。( [83a81170](https://github.com/swoft-cloud/swoft-component/pull/456/commits/83a8117047036da5687fa6dc5236301716dfd59f) )
*   优化启动 ws 服务器并添加要启动的 rpc 服务器。信息面板不显示 rpc 服务器信息。( [3d1d0d848](https://github.com/swoft-cloud/swoft-component/pull/454/commits/3d1d0d848a8f5ca31a09feb5ee860079f7c018e4) )

额外:

*   文档添加支持 google 搜索
*   新阿波罗组件
*   新咨询组件
*   新断路器组件
*   新 limter 组件

## 优雅的服务治理

Swoft 官方推荐开发者使用服务网状模式，比如 Istio/Envoy 框架，来分离业务和服务治理，但 Swoft 也提供了一套微服务组件，供中小型企业快速构建微服务。

*   [服务注册和发现](https://en.swoft.org/docs/2.x/en/ms/govern/register-discovery.html)
*   [服务断路器](https://en.swoft.org/docs/2.x/en/ms/govern/breaker.html)
*   [服务限制器](https://en.swoft.org/docs/2.x/en/ms/govern/limiter.html)
*   [配置中心](https://en.swoft.org/docs/2.x/en/ms/govern/config.html)

### 服务注册和发现

对于服务注册和发现，需要 swoft 提供的 [swoft-consul](https://en.swoft.org/docs/2.x/en/extra/consul.html) 组件，如果其他第三方类似的话。

#### 注册和注销服务

监听`SwooleEvent::START`事件，注册服务

```
/**
 * Class RegisterServiceListener
 *
 * @since 2.0
 *
 * @Listener(event=SwooleEvent::START)
 */
class RegisterServiceListener implements EventHandlerInterface
{
    /**
     * @Inject()
     *
     * @var Agent
     */
    private $agent;

    /**
     * @param EventInterface $event
     */
    public function handle(EventInterface $event): void
    {
        /* @var HttpServer $httpServer */
        $httpServer = $event->getTarget();

        $service = [
            // ....
        ];

        $scheduler = Swoole\Coroutine\Scheduler();
        $scheduler->add(function () use ($service) {
            // Register
            $this->agent->registerService($service);
            CLog::info('Swoft http register service success by consul!');
        });
        $scheduler->start();
    }
} 
```

监听`SwooleEvent::SHUTDOWN`事件，取消服务

```
/**
 * Class DeregisterServiceListener
 *
 * @since 2.0
 *
 * @Listener(SwooleEvent::SHUTDOWN)
 */
class DeregisterServiceListener implements EventHandlerInterface
{
    /**
     * @Inject()
     *
     * @var Agent
     */
    private $agent;

    /**
     * @param EventInterface $event
     */
    public function handle(EventInterface $event): void
    {
        /* @var HttpServer $httpServer */
        $httpServer = $event->getTarget();

        $scheduler = Swoole\Coroutine\Scheduler();
        $scheduler->add(function () use ($httpServer) {
            $this->agent->deregisterService('swoft');
        });
        $scheduler->start();
    }
} 
```

#### 服务发现

定义服务提供商

```
/**
 * Class RpcProvider
 *
 * @since 2.0
 *        
 * @Bean()
 */
class RpcProvider implements ProviderInterface
{
    /**
     * @Inject()
     *
     * @var Agent
     */
    private $agent;

    /**
     * @param Client $client
     *
     * @return array
     * @example
     * [
     *     'host:port'
     * ]
     */
    public function getList(Client $client): array
    {
        // Get health service from consul
        $services = $this->agent->services();

        $services = [

        ];

        return $services;
    }
} 
```

配置服务提供商

```
return [
    'user'           => [
      'class'   => ServiceClient::class,
      'provider' => bean(RpcProvider::class)
      // ...
    ]
]; 
```

### 服务断路器

Swoft 使用`@Breaker`注释来实现一个 blow，可以在任何方法上 blow。

```
/**
 * Class BreakerLogic
 *
 * @since 2.0
 *
 * @Bean()
 */
class BreakerLogic
{
    /**
     * @Breaker(fallback="funcFallback")
     *
     * @return string
     * @throws Exception
     */
    public function func(): string
    {
        // Do something

        throw new Exception('Breaker exception');
    }

    /**
     * @return string
     */
    public function funcFallback(): string
    {
        return 'funcFallback';
    }
} 
```

### 服务限制

Swoft 使用`@RateLimiter`注释实现服务节流，可以在任何方法上节流，不仅仅是控制器，KEY 还支持[symfony/expression-language](https://symfony.com/doc/current/components/expression_language/syntax.html#component-expression-objects)表达式语言。

```
/**
 * Class LimiterController
 *
 * @since 2.0
 *
 * @Controller(prefix="limiter")
 */
class LimiterController
{
    /**
     * @RequestMapping()
     * @RateLimiter(key="request.getUriPath()", fallback="limiterFallback")
     *
     * @param Request $request
     *
     * @return array
     */
    public function requestLimiter(Request $request): array
    {
        $uri = $request->getUriPath();
        return ['requestLimiter', $uri];
    }

    /**
     * @param Request $request
     *
     * @return array
     */
    public function limiterFallback(Request $request): array
    {
        $uri = $request->getUriPath();
        return ['limiterFallback', $uri];
    }
} 
```

### 配置中心

配置中心需要使用 Swoft 官方提供的 [Swoft-apollo](https://en.swoft.org/docs/2.x/en/extra/apollo.html) 组件，如果其他第三方类似。

#### 申报代理人

```
/**
 * Class AgentCommand
 *
 * @since 2.0
 *
 * @Command("agent")
 */
class AgentCommand
{
    /**
     * @Inject()
     *
     * @var Config
     */
    private $config;

    /**
     * @CommandMapping(name="index")
     */
    public function index(): void
    {
        $namespaces = [
            'application'
        ];

        while (true) {
            try {
                $this->config->listen($namespaces, [$this, 'updateConfigFile']);
            } catch (Throwable $e) {
                CLog::error('Config agent fail(%s %s %d)!', $e->getMessage(), $e->getFile(), $e->getLine());
            }
        }
    }

    /**
     * @param array $data
     *
     * @throws ContainerException
     * @throws ReflectionException
     */
    public function updateConfigFile(array $data): void
    {
        foreach ($data as $namespace => $namespaceData) {
            $configFile = sprintf('@config/%s.php', $namespace);

            $configKVs = $namespaceData['configurations'] ?? '';
            $content   = '<?php return ' . var_export($configKVs, true) . ';';
            Co::writeFile(alias($configFile), $content, FILE_NO_DEFAULT_CONTEXT);

            CLog::info('Apollo update success！');

            /** @var HttpServer $server */
            $server = bean('httpServer');
            $server->restart();
        }
    }
} 
```

#### 启动代理

代理只需要在服务(Http/RPC/Websocket)启动之前运行。

```
php bin/swoft agent:index 
```

## 资源

*   GitHub:[https://github.com/swoft-cloud/swoft](https://github.com/swoft-cloud/swoft)
*   网址:[https://en.swoft.org](https://en.swoft.org)
*   文件:[https://en.swoft.org/docs](https://en.swoft.org/docs)
*   讨论:[微软云/社区](https://gitter.im/swoft-cloud/community)