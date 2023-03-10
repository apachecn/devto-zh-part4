# 在 PHP 中保持凭证安全

> 原文：<https://dev.to/enygma/keeping-credentials-secure-in-php-1mgo>

** [之前在我的网站 Websec.io 上发布的](https://websec.io/2018/06/14/Keep-Credentials-Secure.html)

任何类型的应用程序(不仅仅是 web 应用程序)中最困难的事情之一是如何保护“秘密”值。这些值可能是 API 密钥、数据库密码，甚至是特殊的旁路代码。理想情况下，您不必直接在应用程序中定义它们，并且可以从其他来源加载它们。

虽然围绕保护秘密的许多问题可以通过更好的秘密处理来消除，但似乎仍然需要在应用程序中存在某种秘密值。显然，不建议使用这种模式。常见弱点列举数据库甚至有一个专门关于它的条目: [CWE-798](https://cwe.mitre.org/data/definitions/798.html) 。如果攻击者能够以某种方式访问代码并直接读取它们，那么硬编码凭据(尤其是纯文本凭据)会带来巨大的风险。

## 那么 PHP 呢？

在 PHP 应用程序中，有一种常见的模式是将配置值和访问细节保存在一个`.env`文件中，该文件位于 PHP 应用程序可以访问的地方。鉴于这是一种常见的做法，我不想偏离它太远。我想在这里提供一些有用的东西，可以很容易地取代这种设置，同时尽可能保持简单。

我将介绍几种凭据存储方法，花一些时间讨论它是什么以及它的优缺点。它们都将利用简单的存储方法，要么基于代码，要么基于相关的平面文件(如`.env`)。我们将从最糟糕的方法开始——在代码中存储纯文本凭证。

### 将凭证放入代码中

作为开发人员，很容易想到，既然您需要凭据，比如说，通过 HTTP 客户端连接到 API，那么让凭据尽可能靠近代码是最简单、最好的解决方案。这种方法有两个大问题:

1.  这些硬编码的凭据现在将存在于您的版本控制中。如果它在一个公共的 GitHub 库上，你基本上只是将这些凭证暴露给任何可以克隆这个库的人。
2.  如果攻击者能够访问您的应用程序的源代码，他们就可以直接访问这些秘密，而无需进行任何类型的解密或逆向工作。

> **重要提示:**如果你的文件中有硬编码的信用凭证，立即将它们重构出来。这是一个糟糕的安全实践(在 [CWE-256](https://cwe.mitre.org/data/definitions/256.html) 和 OWASP Top 10 (as A2)的[破解认证](https://www.owasp.org/index.php/Top_10-2017_A2-Broken_Authentication)项目中都有提及)。

在过去的几年里，有几个例子，一些是 web 应用程序，一些是其他类型的应用程序/硬件，默认或硬编码的密码是它们失败的原因。不惜一切代价避免这种情况！有一个非常非常*非常*纤细的用例，可以在你的代码中直接包含任何类型的敏感信息。即使这样，通常还有其他内置的保护方法，以确保这些方法只能在少数情况下使用。

### `.env`文件活在文档根

所以，现在我们已经确定我们需要在代码中避免硬编码的纯文本凭证，我们需要找出另一种方法来存储它们。这就是流行的`.env`文件的用武之地。自从更现代的 PHP 开发时代到来后(感谢像 [Composer](https://getcomposer.org) 这样的工具)，许多框架和库都采用了使用`.env`文件来存储特定于应用程序的设置的模式。很自然，这意味着最终秘密和敏感信息会被发现。

将所有这些放在一个单独的文件中比硬编码要好，但是仍然存在一些问题。如果您重新阅读本节的标题，您可能会发现问题。请记住，外部世界可以直接读取文档根目录中的任何内容。在这种情况下，选择将`.env`文件放在文档根目录中。这意味着我可以点击`http://mycoolsite.com/.env`并能够直接访问这个文件。

所以，我们可以把这个从列表中删除，作为一个存储我们需要保护的任何东西的方法。然而，还有另一个类似的选项可以阻止直接访问:将`.env`文件移出文档根目录。

### `.env`文件活在文档根之外

在这种情况下，我们将文件上移一层，这样就不能从 web 上直接访问它。例如，如果您的文档根是`/var/www/mycoolsite/public`，那么您可以在`/var/www/mycoolsite/.env`将文件向上移动一级。这使得 PHP 仍然可以访问该文件，但不能通过 web 访问。

例如，我们可以使用流行的 [vlucas/phpdotenv](https://github.com/vlucas/phpdotenv) 包来读取文件，并自动将其导入当前环境:

```
<?php
require_once __DIR__.'/../vendor/autoload.php';

$dotenv = new Dotenv\Dotenv(__DIR__.'/../');
$dotenv->load();
?> 
```

在这个脚本中，代码被告知从上一个目录(`__DIR__.'/../'`)加载`.env`文件。然后，它获取键/值组合，并将它们放入`$_ENV`超全局。这种方法比公开访问文件要好，但是也有一些缺点需要考虑:

1.  如果攻击者能够上传 PHP 文件并执行代码，他们可以打印出`$_ENV`值并直接访问。
2.  这些值仍然以纯文本形式存在于磁盘上，因此如果发现本地文件包含问题，仍然可以读取该文件。

因此，这种方法是朝着正确方向迈出的一步，但我们仍然可以使用更强大的工具来保护我们的凭据。

### 加密凭证

防止直接访问秘密值的下一步是使用混淆或加密的方法来保护值。因为我们需要值的纯文本版本来实际使用它，所以混淆是不可能的。使用加密，我们可以加密值，然后在需要时解密。

我们将建立在前一个例子的基础上，将这些值放在位于文档根目录之外的一个`.env`文件中。为了帮助简化加密/解密过程，我们将使用 [defuse/php-encryption](https://github.com/defuse/php-encryption) 库。

首先，我们需要安装它并生成一个密钥:

```
composer require defuse/php-encryption
vendor/bin/generate-defuse-key 
```

这将给出一个包含大小写字母和数字的密钥，并且有足够的熵用于这个简单的操作。这个密钥需要存储在 PHP 可以访问的地方，而不是应用程序的文档根目录中的某个地方(甚至是附近)。通常的做法是将它放在平面文件中的`/usr/local`下。然后，这个文件需要更改权限和所有者/组，以便 PHP 可以读取它。

一旦建立了这个文件，就可以从`.env`文件中读取并解密这些值。我们将使用同一个`vlucas/phpdotenv`库读入文件，然后用`php-encryption`解密。先举个例子`.env`文件:

```
test=def502003cbef858698bc40b2b8d0ffb6f365f2cef00009047650910941da72372313c7ce3f9d4ce8ba2cd64f6a5a5a330da47151c5c90124fd4e8ea792d40810d8906b8a888b12db78f1cbb0819825447ce685b1c608dfb1f30
test1=def502005c647492189c68d7f5fec781a0e10bdee8865b23f729b080c7bbadd2204005367ea6464d75609ea48be235886cd2f398bf60eaa0a0bb32e2906ab9b9b1f66c58fdd24f054b5311460fdf8770c5d729b3c296cb5d 
```

然后 PHP 来解密它:

```
<?php
require_once __DIR__.'/../vendor/autoload.php';

$dotenv = new Dotenv\Dotenv(__DIR__.'/../');
$dotenv->load();

$keyContents = file_get_contents('/usr/local/keyfile`);
$key = \Defuse\Crypto\Key::loadFromAsciiSafeString($keyContents);

$secret = \Defuse\Crypto\Crypto::decrypt($ciphertext, $key);
?> 
```

然后，我们解密的秘密值在`$secret`变量中结束。显然，您不希望到处复制和粘贴这些代码，这样将它包装在一个帮助器函数或类中会更简单，使它更加独立。

这是在保护我们的凭证的正确方向上迈出的又一步，但是这里仍然有一个所有这些平面文件存储方法共有的问题:本地文件包含。如果攻击者能够让您的代码读取并暴露文件内容，这意味着它不仅可以读取来自`.env`的加密值，还可以读取`keyfile`的内容，因为 PHP 也需要能够读取这些内容。

我们已经别无选择了，但让我再建议一个。这种方法仍然允许您将值存储在平面文件中，但是保护它们免受本地文件包含攻击，因为 PHP 不需要访问包含它们的文件，只需要访问 Apache web 服务器。让我们开始吧。

## “阿帕奇拉”法

在这个方法中，我们将使用一些与以前相同的技术(将加密的秘密存储在平面文件中),但是有一个新的变化:利用 Apache 环境变量将这些值传递给 PHP。

> 注意:本教程展示了如何设置 Apache web 服务器，但是同样的方法也可以通过 Nginx 来实现。

如果您想要快速简单的版本，我已经用一个基于 Docker 的示例建立了这个库,展示了环境需要如何配置。

代码和应用程序将遵循以下步骤:

1.  在文件系统的某个地方创建了一个包含加密凭证的文件(在本例中，我们只是将它放在`/tmp`中)
2.  然后这个文件在`/etc/apache2/envvars`文件中作为一个额外的源，将这些值作为本地环境变量拉进来。
3.  当 Apache 启动时，它从`envvars`获取所有的值，并在内部重新定义它们。这包括我们特殊的价值观。
4.  这些值通过一个`SetEnv`语句经由 Apache 环境变量被推送到 PHP。

您现在可能会问的问题与那些讨厌的本地文件包含问题有关。附加设置还是不能被 PHP 读取吗？这就是拼图的最后一块:配置。使用 PHP，您可以使用`open_basedir`来设置 PHP 可以与之交互的目录，并防止它超出这些目录。在这种情况下，我们可以将 PHP 锁定在文档根目录，防止它手动获取文件。

在我继续展示它是如何工作的之前，我想说一件事——这个解决方案也不是完美的。如果攻击者能够执行 PHP 代码并从`$_ENV`超全局中读取数据，键值仍然会被暴露。

### 秘密

首先，我们将设置秘密并获得正确的来源。在我们的`/tmp/addl-settings`文件中，我们已经定义了键值:

```
export ENC_KEY=1234567890 // This is just a sample key, obviously 
```

现在我们将这个文件放在文件底部的`envvars`文件中:

```
. /tmp/addl-settings 
```

设置好之后，Apache 会将`ENC_KEY`值加载到它的内部环境中，并使它可用。

### Apache Config

接下来是 Apache 配置。在这种情况下，我们将利用虚拟主机，但是您也可以在基础级别上这样做:

```
<VirtualHost *:80>
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/html

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined

    SetEnv ENC_KEY ${ENC_KEY}
</VirtualHost> 
```

在这个配置中，您可以看到一个特殊的`${}`概念，该概念用于引入一个 Apache 环境变量，并使其对正在运行的进程可用。在 PHP 中，这意味着在`$_ENV`中将它设为一个值。

### Open_basedir

拼图的最后一步是设置 [open_basedir](http://php.net/manual/en/ini.core.php#ini.open-basedir) 保护，因此我们创建一个`open_basedir.ini`文件，并将其复制到正确的位置，以便 Apache 将其作为 PHP ini 配置文件读取:

```
open_basedir=/var/www/html 
```

有了所有这些，PHP 现在可以通过 Apache 访问`ENC_KEY`值——我们的加密密钥，但是不能作为文件直接访问。

## 但是等等，还有！

这个设置很棒，但是你可能会问自己“现在如何读取我的加密配置设置”？好吧，在一个方便的库- [psecio/secure_dotenv](https://github.com/psecio/secure_dotenv) 的帮助下，这个库负责很多处理工作，这就简单多了。我们已经有了在环境中进行加密和解密所需的密钥，因此我们将在这些示例中重用它。首先，使用[作曲](https://getcomposer.org) :
安装包

```
composer require psecio/secure_dotenv 
```

然后，在您的应用程序中，使用到密钥文件:
的路径在环境变量中创建新的`Parser`实例

```
<?php
$envFile = __DIR__.'/.env';
$parser = new \Psecio\SecureDotenv\Parser($_ENV['ENC_KEY'], $envFile);
?> 
```

重用上面例子中的`.env`文件为我们提供了`test`和`test1`的值，这些值可以从`getContent`调用的结果中提取。

```
<?php
echo 'test1 is: '.$parser->getContent()['test1'];
?> 
```

该库在幕后为您处理值的解密(使用同一个 [defuse/php-encryption 库](https://github.com/defuse/php-encryption)),留给您一个纯文本结果。

## 总结

保护 PHP 应用程序中的秘密是一个需要解决的有趣问题。在撰写本文之前的研究中，我发现——就像任何其他与安全相关的主题一样——完成一项任务总有多种方法。PHP 使得这变得更加困难，因为它与 web 服务器的交互方式。PHP 脚本和处理至少需要对它们需要处理的每个文件有读取权限。如果不小心的话，这使得防止本地文件包含问题变得非常困难。

没有任何 100%安全的凭证存储选项，但我在这里分享的这种“Apache pull”方法是一种更简单的方法，它不需要比您已经在使用的技术更多的东西。当然，如果您有一个使用 Chef、vagger 或其他工具部署的更复杂的环境，这些工具附带了一些附加功能(如加密的 Chef 数据标签)，也可以用于凭证处理。

请记住，对此没有“一刀切”的解决方案。这在很大程度上取决于您的环境和应用程序的风险要求。在决定如何保护您的秘密之前，请务必坐下来为您的应用程序创建一个准确的[威胁模型](https://www.owasp.org/index.php/Application_Threat_Modeling)。这将让你对自己的需求以及需要何种保障有一个更好的总体了解。

### 资源

*   [化解/php 加密](https://github.com/defuse/php-encryption)
*   [psecio/secure_dotenv](https://github.com/psecio/secure_dotenv)
*   [open_basedir](http://php.net/manual/en/ini.core.php#ini.open-basedir)
*   [vlucas/phpdotenv](https://github.com/vlucas/phpdotenv)
*   [“Apache pull”受保护环境的示例](https://github.com/psecio/protected-env-example)(使用 Docker)
*   其他凭证存储选项:[https://gist . github . com/maxvt/bb 49 a6 c 7243163 b 8120625 fc 8 AE 3 F3 CD](https://gist.github.com/maxvt/bb49a6c7243163b8120625fc8ae3f3cd)