# 设计模式:简单工厂

> 原文：<https://dev.to/lasotaartur/design-patterns-simple-factory-8m4>

简单工厂是创造性的设计模式之一。它创建具有相同接口的对象。具体对象的创建依赖于方法中的参数。

# 举例:

假设我们喜欢根据环境对应用程序进行不同的配置。所以我们创建了两个类:

```
class ConfigProd implements ConfigInterface
{
    public function getDatabaseName(): string
    {
        return 'database_prod';
    }
} 
```

```
class ConfigDev implements ConfigInterface
{
    public function getDatabaseName(): string
    {
        return 'database_dev';
    }
} 
```

每个类实现相同的接口:

```
interface ConfigInterface
{
    public function getDatabaseName(): string;
} 
```

当然，我们有一个简单的工厂:

```
class ConfigFactory
{
    public function createConfig(string $environment): ConfigInterface
    {
        switch ($environment) {
            case 'dev':
                return new ConfigDev();
            case 'prod':
                return new ConfigProd();
        }
    }
} 
```

如果我们需要生产配置，那么我们将字符串`prod`传递给工厂方法，我们得到生产配置。这同样适用于开发环境。

```
$configProd = (new ConfigFactory())->createConfig('prod');
$configDev = (new ConfigFactory())->createConfig('dev'); 
```

所以都是。

工厂中的逻辑可能更复杂，但我们不需要担心这一点。我们只在需要的地方进行配置。

很难找到好的例子。如果你有好的，请在评论中告诉我。谢了。

来源:
[https://medium . com/nested if/Java-simple-factory-pattern-9c 2538 DD 0265](https://medium.com/nestedif/java-simple-factory-pattern-9c2538dd0265)

源代码:
[https://github . com/lasotaartur/design-patterns/tree/master/creation/simply factory](https://github.com/lasotaartur/design-patterns/tree/master/Creational/SimplyFactory)