# 走鹃和 Zend 表达和循环形式。不允许 php 死亡。

> 原文：<https://dev.to/sergey_telpuk/roadrunner-zend-expressive-cycle-orm-not-allow-to-php-to-die-37p>

大家好！本文致力于在 Roadrunner(RR)下运行 PHP 应用程序。如果你还没熟悉 RR，我提出来参观[环节](https://github.com/spiral/roadrunner)。
在我过去的[文章](https://dev.to/sergey_telpuk/zend-expressive-and-cycle-orm-1jkg)中，我试图描述如何容易地将 Cycle ORM 集成到 ZE 中，但这并不像建立以下堆栈那样有趣:Roadrunner、Zend Expressive、Cycle ORM。也许它会给你另一种方法来开始你的新应用程序。ZE 在 PHP 开发人员中不是很受欢迎。在我看来，这是低估了微观框架，因为有选择泽。所以，去吧！

我不喜欢设置 local-env，所以我倾向于使用 docker。我在 docker-compose 的帮助下准备了运行 RR 的库，可以通过下面的[链接](https://github.com/sergey-telpuk/roadrunner-docker-skeleton)找到。

在克隆了走鹃-码头工人-骷髅之后。我们可以使用 Makefile 进行构建和运行。
第一步，运行 Makefile 命令:

```
make build 
```

注意:不要忘记将 sample.env 复制到. env 中。

对于下一步，应该安装 ZE 骨架应用:

```
docker-compose run composer create-project zendframework/zend-expressive-skeleton expressive 
```

下一步，将文件 expressive/public/index . PHP:

```
<?php declare(strict_types=1);

chdir(dirname(__DIR__));
require 'vendor/autoload.php';

return (function () {
    $container = require 'config/container.php';
    $app = $container->get(\Zend\Expressive\Application::class);
    $factory = $container->get(\Zend\Expressive\MiddlewareFactory::class);
    (require 'config/pipeline.php')($app, $factory, $container);
    (require 'config/routes.php')($app, $factory, $container);

    return $app;
})(); 
```

下一步，更改文件 worker.php:

```
<?php declare(strict_types=1);

ini_set('display_errors', 'stderr');

chdir(dirname(__DIR__));
require 'vendor/autoload.php';

$app = require 'expressive/public/index.php';

$relay = new Spiral\Goridge\StreamRelay(STDIN, STDOUT);
$psr7 = new Spiral\RoadRunner\PSR7Client(new Spiral\RoadRunner\Worker($relay));

while ($req = $psr7->acceptRequest()) {
    try {
        $response = $app->handle($req);
        $psr7->respond($response);
    } catch (\Throwable $e) {
        $psr7->getWorker()->error((string)$e);
    }
} 
```

用于在 ZE 下使用 Cycle ORM 来跟踪[链接](https://dev.to/sergey_telpuk/zend-expressive-and-cycle-orm-1jkg)。
完成一步就是运行我们的 RR:

```
make up 
```

尽情享受， [http://localhost:8080](http://localhost:8080)