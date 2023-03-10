# 使用 Hoplite 在 Kotlin 中自由配置样板文件

> 原文：<https://dev.to/sksamuel/boilerplate-free-config-in-kotlin-using-hoplite-6ge>

TL；DR:将 Yaml、Json 等配置文件加载到 Kotlin 数据类中，没有样板文件，并且使用 [Hoplite](https://github.com/sksamuel/hoplite) 进行错误检查。

### 你好 Kotliners！

对于我们这些喜欢用功能性更强的方式开发并避免使用 Spring 的人来说，我们发现自己正在寻找一种方法来处理应用程序中的配置。

最基本的方法是使用 java 属性文件并使用`key=value`对，或者我们可以选择使用流行的 [Lightbend 配置库](https://github.com/lightbend/config)，并在他们的 Json 超集中编写配置，称为 Hocon。无论我们选择什么，我们最终都会编写如下代码:

`val jdbcUrl = config.getString("jdbc.url")`

换句话说，通过引用键从配置中提取值。当调用点需要配置值时，可以提取它们，或者我们可以选择将所有的配置值放入某个中央对象中，并传递它们。无论哪种方式，都不能保证我们引用的键确实存在，除非需要配置。

更糟糕的是，如果配置中的值与目标类型不兼容，我们可能会遇到转换问题。我们都曾被一个错别字咬过，我们将`:8080`作为我们的端口号，但在部署 10 分钟后发现我们得到了一个数字格式异常。

一些库可能支持自动编组到一些基本类型，如 doubles 和 bools，但通常不超过基本原语和集合。

### 使用数据类

我们应该将配置推入类中，而不是通过键将配置取出。

我们首先声明一个数据类(包括嵌套类)。

```
data class Database(val host: String, val port: Int, val database: String)
data class WebServer(val resources: Path, val port: Int, val timeout: Duration) 
data class Config(val env: String, val db: Database, val server: WebServer) 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们用 Yaml、Json、Toml、Hocon 或 Java Props 编写配置。我将在这里使用 Yaml，在一个名为`staging.yaml`
的文件中

```
env: staging

db:
  host: staging.wibble.com
  port: 3306
  database: userprofiles

server:
  port: 8080
  resources: /var/www/web
  timeout: 10s 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们使用`ConfigLoader`类将配置直接编组到我们的配置类中。

`val cfg = ConfigLoader().loadConfigOrThrow<Config>("/staging.yaml")`

现在,`cfg`实例是配置文件的一个完全膨胀的版本，所有的值都被转换成定义的类型。

注意:文件应该在类路径中。如果我们愿意，我们可以使用`Path`的实例从类路径之外的文件加载。

### 容易出错

正如我们在开始时提到的，配置的最大问题之一是当事情出错时。有了 Hoplite，配置一整理好，错误就会漂亮地显示出来。

让我们使用和以前一样的配置，但是这次使用一个有很多错误的文件:

```
envvv: staging

db:
  host: staging.wibble.com
  port: .3306
  databas: userprofiles

server:
  port: localhost
  resources: /var/www/web
  timeout: 10ty 
```

Enter fullscreen mode Exit fullscreen mode

使用该文件，给出以下错误输出:

```
Exception in thread "main": Error loading config because:

    - Could not instantiate 'com.example.Config' because:

        - 'env': Missing from config

        - 'db': - Could not instantiate 'com.example.Database' because:

            - 'host': Missing from config

            - 'port': Could not decode .3306 into a Int (/foo.yml:4:8)

            - 'database': Missing from config

        - 'server': - Could not instantiate 'com.example.WebServer' because:

            - 'port': Could not decode localhost into a Int (/foo.yml:8:8)

            - 'timeout': Required type java.time.Duration could not be decoded from a String value: 10ty (/foo.yml:10:11) 
```

Enter fullscreen mode Exit fullscreen mode

您可以看到调试这个文件是多么容易——详细的错误消息显示了确切的键，可能的话还包括行号和文件名。这些错误包括无法转换为数字的值、缺失值和持续时间的错误格式。

### 支持的类型

Hoplite 支持许多不同类型的开箱即用的电池，就像酷孩子说的那样。这些是你通常的原始类型、集合、枚举、日期、持续时间、`BigDecimal`、`BigInteger`、`UUID`、文件、路径等等。此外，还支持来自[箭头](https://arrow-kt.io/) - `NonEmptyList`、元组和`Option`的数据类型。

如果您愿意，也可以很容易地添加对自定义类型的支持。只需实现`Decoder`接口并通过服务加载器添加它。

还有很多关于重装步兵的内容，所以我会给你提供进一步阅读的官方文档。