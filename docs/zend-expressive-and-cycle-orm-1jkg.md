# Zend 表现形式和循环形式。

> 原文：<https://dev.to/sergey_telpuk/zend-expressive-and-cycle-orm-1jkg>

你好，社区。我决定写一篇短文献给[Zend Expressive](https://docs.zendframework.com/zend-expressive/)&[Cycle ORM(Cycle ORM)](https://github.com/cycle/orm)。我们继续吧！
设置 ZE 可以使用[框架应用和安装程序](https://github.com/zendframework/zend-expressive-skeleton)。这是开始与 ZE 合作的一种简单方式。之后，添加 CycleORM 依赖关系:

```
composer require cycle/orm
composer require cycle/annotated
composer require cycle/schema-builder 
```

下一步，应该添加一个文件夹(如“循环工厂”或其他名称):

```
project    
│
└───cycle-factories
│      └───CycleFactory.php
       └───TransactionFactory.php 
```

应该改变 composer-autoload，可以看起来像:

```
 "autoload":  {  "psr-4":  {  "App\\":  "src/App/src/",  "Cycle\\":  "cycle-factories/"  }  }, 
```

CycleFactory.php

```
<?php declare(strict_types=1);

namespace Cycle;

use Cycle\Annotated\{Embeddings, Entities};
use Cycle\ORM\{Factory, ORM, ORMInterface, Schema as OrmSchema};
use Cycle\Schema\{Compiler, Registry};
use Cycle\Schema\Generator\{GenerateRelations,
    GenerateTypecast,
    RenderRelations,
    RenderTables,
    ResetTables,
    SyncTables,
    ValidateEntities};
use Psr\Container\ContainerInterface;
use Spiral\Database\{DatabaseManager};
use Spiral\Database\Config\{DatabaseConfig};
use Spiral\Database\Exception\{ConfigException};
use Spiral\Tokenizer\ClassLocator;
use Symfony\Component\Finder\Finder;

class CycleFactory
{
    /**
     * @param ContainerInterface $container
     * @return ORMInterface
     */
    public function __invoke(ContainerInterface $container): ORMInterface
    {
        $config = $container->has('config')
            ? $container->get('config')
            : [];

        if (!isset($config['cycle'])) {
            throw new ConfigException('Expected config databases');
        }
        $config = $config['cycle'];
        $entities = $config['entities'];
        $finder = (new Finder())->files()->in($entities);
        $cl = new ClassLocator($finder);
        $dbal = new DatabaseManager(new DatabaseConfig($config));
        $schema = (new Compiler())->compile(new Registry($dbal), [
            new Embeddings($cl),     // register embeddable entities
            new Entities($cl),       // register annotated entities
            new ResetTables(),       // re-declared table schemas (remove columns)
            new GenerateRelations(), // generate entity relations
            new ValidateEntities(),  // make sure all entity schemas are correct
            new RenderTables(),      // declare table schemas
            new RenderRelations(),   // declare relation keys and indexes
            new SyncTables(),        // sync table changes to database
            new GenerateTypecast(),  // typecast non string columns
        ]);
        $orm = new ORM(new Factory($dbal));
        $orm = $orm->withSchema(new OrmSchema($schema));
        return $orm;
    }
} 
```

TransactionFactory.php

```
<?php declare(strict_types=1);

namespace Cycle;

use Cycle\ORM\ORMInterface;
use Cycle\ORM\Transaction;
use Cycle\ORM\TransactionInterface;
use Psr\Container\ContainerInterface;

class TransactionFactory
{
    /**
     * @param ContainerInterface $container
     * @return TransactionInterface
     */
    public function __invoke(ContainerInterface $container): TransactionInterface
    {
        return new Transaction($container->get(ORMInterface::class));
    }
} 
```

对于下一步，应该添加一个配置文件:

```
project    
│
└───config
│   │
│   └───autoload
│       └───cycle.global.php(cycle.local.php)
│       │   ... 
```

cycle.global.php 可以是这样的(另一个配置遵循[文档链接](https://github.com/cycle/docs/blob/master/basic/connect.md) ):

```
<?php declare(strict_types=1);

use Spiral\Database;

return [
    'cycle' => [
        'default' => 'default',
        'databases' => [
            'default' => ['connection' => 'sqlite']
        ],
        'connections' => [
            'sqlite' => [
                'driver' => Database\Driver\SQLite\SQLiteDriver::class,
                'connection' => 'sqlite:database.db',
                'username' => '',
                'password' => '',
            ]
        ],
        'entities' => [//array of Entities 
            'src/App/src/Entities'//it depends on a structure of the project
        ]
    ]
]; 
```

对于下一步，应该添加全局依赖:

```
project    
│
└───config
│   │
│   └───autoload
│       └───dependencies.global.php(dependencies.local.php)
│       │   ... 
```

dependencies.global.php 可以长什么样:

```
<?php declare(strict_types=1);

return [

      ...

        'factories'  => [
            \Cycle\ORM\ORMInterface::class => \Cycle\CycleFactory::class,
            \Cycle\ORM\TransactionInterface::class => \Cycle\TransactionFactory::class,
        ],
    ],
]; 
```

为了测试 Cycle ORM，请查看附加的截图[，使用示例](https://a.imge.to/2019/07/04/30qRr.png)。