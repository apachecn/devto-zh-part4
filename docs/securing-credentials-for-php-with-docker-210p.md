# 用 Docker 保护 PHP 的凭证

> 原文：<https://dev.to/enygma/securing-credentials-for-php-with-docker-210p>

** [之前在我的网站 Websec.io 上发布的](https://websec.io/2018/07/22/Docker-Secure-Credentials.html)

在[之前的一篇文章](https://dev.to/enygma/keeping-credentials-secure-in-php-1mgo)中，我介绍了一种可以用来保护 PHP 应用程序中凭证的方法。在那篇文章中，我提供了一个使用 Apache 及其`envvars`处理读入值并将它们作为`$_ENV`变量传递给等待的 PHP 进程的例子。这与[PSE CIO/secure _ dotenv 库](https://github.com/psecio/secure_dotenv)相结合，允许您传递一个加密密钥，该密钥可用于解密应用程序的`.env`文件中的值。

虽然这适用于一个扁平的 Apache 和 PHP 环境，但世界已经超越了这个基本设置，转而使用另一个流行的环境构建工具: [Docker](https://www.docker.com/) 。Docker 使我们有可能用专门用于特定目的的互连容器来创建环境。这使得用新版本替换堆栈中的技术变得更加容易，并使容器更加可重用。只需一个简单的配置文件就可以设置一个环境，并且可以随时用来重建它。

因此，如果我们继续使用当前的技术，我们需要一种方法在基于 Docker 的环境中保护我们的凭证，这种方法利用了 PHP-FPM 和 Nginx。幸运的是，有一个相对简单的方法来处理这个问题，只需做一些配置更改。为了使设置更加健壮，我还将向您展示如何将 [Vault](https://www.vaultproject.io/) 集成到秘密存储流程中。

Vault 是 Hashicorp 的一个项目，专门用于保护秘密值。为了能够访问这些值，您可以“解锁”服务，然后通过 API 获取您的秘密值。完成后，您可以“锁定”服务备份，防止没有锁定/解锁权限的任何人获得秘密值。

## 环境概述

在我们深入了解实现这一切所需的配置和文件之前，我想简要了解一下这里涉及的各个部分，以及它们是如何组合在一起的。我在上面顺便提到了其中的一些，但这里的列表都在一个地方:

1.  **Docker** 构建和管理环境
2.  Nginx 处理 web 请求和响应
3.  **PHP-FPM** 解析并执行请求的 PHP
4.  **金库**储存和管理机密

我还将利用[一个简单的 Vault 客户端——PSE CIO/Vault lib](https://github.com/psecio/vaultlib)向 Vault 请求机密。结合使用这些技术和一点配置，一个工作系统并不太难。

## 保护凭证

有几种方法可以将秘密放入基于 Docker 的环境中，有些方法比其他方法更安全。以下是一些选项及其优缺点的列表:

### 将它们作为命令行选项传入

Docker 允许的一个选项是在打开容器时在命令行上传递值。例如，如果您想在一个容器中执行一个命令，并传递成为环境变量的值，那么您可以使用`-e`选项:

```
docker run -e "test=foo" whoami 
```

在这个命令中，我们正在执行`whoami`命令，并传入一个值为`foo`的环境变量`test`。虽然这很有用，但它仅限于在单个命令中使用，而不是在启动时在整个环境中使用。此外，当您在命令行上运行命令时，该命令及其所有参数可能会显示在进程列表中。这将向任何有权访问服务器的人公开变量的纯文本版本。

### 利用 Docker 的“秘密”

列表中最安全的另一个选项是使用 Docker 自己的“秘密”处理。[这个功能](https://docs.docker.com/engine/swarm/secrets/)允许你在一个加密的存储位置存储秘密值，但是仍然允许从 Docker 容器内部访问它们。您可以使用`docker secret`命令来设置该值，并向应该拥有访问权限的服务授予访问权限。他们的[文档](https://docs.docker.com/engine/swarm/secrets/)有几个设置它的例子，以及如何在更真实的情况下使用它(比如 WordPress 博客)。

虽然这个存储选项是较好的选项之一，但它也有一个警告:它只能在 Docker 群集的情况下使用。Docker Swarm 是 Docker 内置的功能，它使管理一个 Docker 实例集群变得更加容易。如果你没有使用群体模式，那么使用这种“秘密”存储方法就太不走运了。

### 将它们硬编码在`docker-compose`配置中

Docker Compose 还有另一个选项可以将值作为变量推入环境:通过`docker-compose.yml`配置文件中的设置。

## 设置

在我开始设置之前，我想概述一下我们将要使用的文件和目录结构。这里涉及到几个配置文件，我想把它们调用出来，这样它们就都就位了。

对于示例，我们将在包含以下文件的`project1/`目录中工作:

*   `docker-compose.yml`
*   `www.conf`
*   `site.conf`
*   `.env`

## 盯着码头工人

首先，我们需要构建应用程序将要生存的环境。这是码头工人的工作，或者更确切地说是[码头工人作曲](https://docs.docker.com/compose/)。对于那些不熟悉 Docker Compose 的人来说，你可以把它看作是 Docker 之上的一个层，它使得构建环境比拥有一堆`Dockerfile`配置文件更简单。它将不同的容器作为“服务”连接在一起，并提供了一个配置结构，该结构抽象出了许多手动命令，而仅仅使用`docker`命令行工具将需要这些手动命令。

在撰写配置文件中，您定义了想要创建的“服务”以及关于它们的各种设置。例如，如果我们只想创建一个简单的服务器，在端口`8080`上运行 Nginx，我们可以创建一个`docker-compose.yml`配置，如下所示:

```
version: '2'

services:
  web:
    image: nginx:latest
    ports:
      - "8080:80" 
```

很简单，对吧？你可以只用`Dockerfile`配置创建同样的东西，但是 Compose 让它变得更简单。

### `docker-compose.yml`配置

使用这种结构，我们将创建一个包括以下内容的环境:

*   一个运行 Nginx 的容器，它将我们的`code/`目录挂载到它的文档根目录
*   一个运行 PHP-FPM (PHP 7)的容器，用于处理传入的 PHP 请求(链接到 Nginx 容器)
*   运行 Vault 服务的 Vault 容器(链接到 PHP 容器)

看起来是这样的:

```
version: '2'

services:
  web:
    image: nginx:latest
    ports:
      - "8080:80"
    volumes:
      - ./code:/code
      - ./site.conf:/etc/nginx/conf.d/site.conf
    links:
      - php

  php:
    image: php:7-fpm
    volumes:
      - ./www.conf:/usr/local/etc/php-fpm.d/www.conf
      - ./code:/code
    environment:
      - VAULT_KEY=${VAULT_KEY}
      - VAULT_TOKEN=${VAULT_TOKEN}
      - ENC_KEY=${ENC_KEY}

  vault:
    image: vault:latest
    links:
      - php
    environment:
      - VAULT_ADDR=http://127.0.0.1:8200 
```

让我们走一遍，这样你就能理解每一部分。首先我们创建了`web`服务——这是我们的 Nginx 容器，它从`nginx:lastest`映像安装。然后定义要使用的端口，设置容器在端口`8080`上响应，并代理到本地机器上的端口`80`(默认端口为`HTTP`)。`volumes`部分定义了从本地系统挂载到远程系统的两个东西:我们的`code/`目录和复制到`/etc/nginx/conf.d/site.conf`的 Nginx 配置路径的`site.conf`。最后，在`links`部分，我们告诉 Docker 我们想要链接`web`和`php`容器，这样它们就能互相知道。这个链接使得 Nginx 配置能够调用 PHP-FPM 作为`*.php`请求的处理程序。文件`site.conf`的内容将在本文后面的章节中解释。

接下来是`php`服务。这个服务从`php:7-fpm`镜像安装，加载使用`7.x`版本的 PHP-FPM 的最新版本。同样，我们有一个`volumes`部分将`code/`复制到容器中，但是这次我们移入了一个不同的配置文件:`www.conf`配置。这是 PHP-FPM 在处理 PHP 请求时使用的配置。关于这个配置的更多信息将在后面分享。

> 你可能会问，`php`服务中的`environment`设置呢？别担心，我稍后会谈到这些，但这些是我们如何从 Docker 获取值并将其推入服务容器供以后使用的关键之一。

最后，我们到达`vault`服务。该服务使用`vault:latest`映像获取最新版本的 Vault 容器，并运行设置过程。还有一个到`php`服务的链接，这样 Vault 和 PHP 就可以对话了。最后一部分是`environment`设置，这只是一个特定于 Vault 的设置，这样我们就知道一个可预测的地址和端口来从 PHP 访问 Vault 服务。

### `site.conf`配置(Nginx)

我之前在浏览`docker-compose.yml`配置时提到过这个配置，但是让我们更深入地了解一下。首先，这里是我们`site.conf`的内容:

```
server {
    index index.php index.html;
    server_name php-docker.local;
    error_log /var/log/nginx/error.log;
    access_log /var/log/nginx/access.log;
    root /code;

    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass php:9000;
        fastcgi_index index.php;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param PATH_INFO $fastcgi_path_info;
    }
} 
```

如果你以前用过 PHP-FPM 和 Nginx，这个配置可能看起来非常相似。它设置服务器配置(主机名为`php-docker.local`，将其添加到`/etc/hosts`中的`127.0.0.1`，通过 FastCGI 将对`.php`脚本的任何请求传递给 PHP-FPM。我们的`index`设置允许我们使用一个`index.php`或`index.html`文件作为基础，而不必在 URL 中指定它。很简单，对吧？

当我们启动 Docker Compose 时，这个配置将被复制到位于`/etc/nginx/conf.d/site.conf`路径的容器中。解决了这个问题，我们将进入下一个文件:PHP-FPM 配置。

### `www.conf`配置(PHP-FPM)

该配置设置了当 Nginx 将传入请求传递给 PHP-FPM 进程时，该进程的行为方式。我已经减少了文件的内容(删除了多余的注释)以使它在这里更清晰。以下是该文件的内容:

```
[www]
user = www-data
group = www-data

listen = 127.0.0.1:9000

pm = dynamic
pm.max_children = 5
pm.start_servers = 2
pm.min_spare_servers = 1
pm.max_spare_servers = 3

clear_env = no

env[VAULT_KEY] = $VAULT_KEY
env[VAULT_TOKEN] = $VAULT_TOKEN 
```

虽然这个配置的大部分是默认设置，但是这里有一些事情需要注意，从`clear_env`行开始。默认情况下，PHP-FPM 不会导入进程启动时设置的任何环境变量。这个被设置为`no`的`clear_env`设置告诉它导入那些值，并使它们可以被 Nginx 访问。在接下来的几行中，有一些值是用`env[]`指令手动定义的。这些变量来自环境，然后作为`$_ENV`值传递给 PHP 进程。

> 如果您注意的话，您可能会注意到配置文件之间的排列方式以及环境变量是如何传递的。

这个配置将被 Compose 复制到`/usr/local/etc/php-fpm.d/www.conf`路径。有了这个文件，我们就可以开始拼图的最后一块了:文件`.env`。

### `.env`配置(环境变量)

Docker Compose 的一个便利之处是它能够在运行 build/up 命令时读取默认的`.env`文件，并自动导入它们。在这种情况下，我们有一些设置不想硬编码到`docker-compose.yml`配置中，也不想硬编码到我们实际的 PHP 代码中:

*   密封/开启保险库的钥匙
*   用于访问 Vault API 的令牌
*   用于加密配置值的密钥

我们可以在基本的`project1/`目录下的`.env`文件中定义这些:

```
VAULT_KEY=[ key to use for locking/unlocking ]
VAULT_TOKEN=[ token to use for API requests] 
```

显然，在创建文件时，您会希望用您的值替换`[...]`字符串。

> **注意:不要在生产环境中使用根令牌和密钥。**此处使用它仅出于示例目的，无需进一步设置和配置要使用的其他凭证的 Vault 实例。有关 Vault 中身份验证方法选项的更多信息，[在其手册中查看本页](https://www.vaultproject.io/docs/auth/index.html)。

这里需要注意的一件棘手的事情是，当您(重新)构建 Vault 容器时，它将从零开始，并将删除您创建的任何用户(甚至重置根密钥/令牌)。这里的关键是，一旦构建了环境，就获取这些值，将它们放入`project1/.env`中，然后重新构建`php`服务，将新的环境值放入:

```
docker-compose build -d php 
```

## 一切都是为了代码

好了，现在我们已经完成了设置环境所需的四个配置文件，接下来我们来谈谈代码。在这种情况下，PHP 代码驻留在`project1/code/`中。因为我们要保持这个超级简单，所以这个例子只有一个文件:`index.php`。代码背后的基本思想是能够从我们的应用程序中需要的 Vault 服务器中提取机密值。因为我们要使用 [psecio/vaultlib](https://github.com/psecio/vaultlib) 库，我们需要通过 Composer:
安装它

```
composer require psecio/vaultlib 
```

如果您在本地系统的`project1/code/`目录中运行该程序，它将设置包含您需要的所有内容的`vendor/`目录。由于`code/`目录是作为卷安装在`php`服务上的，所以当您发出 web 请求时，它会从本地版本中提取它。

安装完成后，我们就可以初始化我们的 Vault 连接并设置我们的第一个值:

```
<?php
require_once __DIR__.'/vendor/autoload.php';

$accessToken = $_ENV['VAULT_TOKEN'];
$baseUrl = 'http://vault:8200';

$client = new \Psecio\Vaultlib\Client($accessToken, $baseUrl);

// If the vault is sealed, unseal it
if ($client->isSealed() == true) {
    $client->unseal($_ENV['VAULT_KEY']);
}

// Now set our secret value for "my-secret"
$result = $client->setSecret('my-secret', ['testing1' => 'foo']);
echo 'Result: '.var_export($result, true);

?> 
```

现在，如果您向端口`8080`上的本地实例发出请求，并且一切顺利，您应该会看到消息“Result: true”。如果您看到异常，可能是容器构建有问题。您可以使用`docker-compose down`销毁所有当前实例，然后使用`docker-compose build; docker-compose up`将它们全部恢复。如果这样做，请确保交换出保险库令牌和密钥，并重建`php`服务。

在上面的代码中，我们创建了一个`Psecio\Vaultlib\Client`的实例，并传入了从环境变量中提取的令牌。这个变量的存在是因为我们的配置文件中有一些特殊的行。流程是这样的:

1.  这些值被设置在`.env`文件中，以便 Docker 获取。
2.  使用`docker-compose.yml`配置中的`environment`部分将这些值推入`php`容器。
3.  PHP-FPM 配置然后导入环境变量，并使它们在`$_ENV`超全局中可用。

这些秘密存在于容器的内存中，不必写入容器本身内部的文件中，在那里它们可能会在静止时被破坏。一旦 Docker 容器启动，就可以删除`.env`文件，而不会影响容器内部的值。

> 这里的棘手之处在于，如果在容器启动并运行后删除了`.env`文件，那么如果需要再次运行`build`命令，就需要将它放回原处。

## 但是为什么这样好呢？

在这篇文章的开头，我给出了一些使用 Docker 时可以用于秘密存储的方法，但是它们都有相当大的缺点。使用这种方法，有一个很大的好处，这是其他方法所没有的:在`.env`文件中定义的秘密只存在于内存中，但是 PHP 进程仍然可以访问。这为它们提供了一个非常重要的保护层，使攻击者更难直接访问它们。

然而，我要说一件事。就像没有什么是 100%安全的一样，这种方法也不是。它确实通过不要求它们静止在某个地方来保护秘密，但是它并不阻止直接访问`$_ENV`值。如果攻击者能够执行远程代码执行攻击——欺骗您的应用程序运行他们的代码——他们将能够访问这些值。

不幸的是，由于 PHP 的工作方式，没有一个很好的内置方法来保护值。这就是 Vault 包含在此环境中的原因。它是专门为存储秘密值和保护它们而设计的。通过只传递令牌和密钥来访问它，我们降低了整个系统的风险级别。Vault 还包括允许您微调设置的访问级别的控件。这将允许您做一些事情，比如创建一个您的应用程序可以使用的只读用户。即使有妥协，至少你的秘密价值不会被改变。

希望通过我在这里提供的代码、配置和解释，您已经成功地建立并运行了一个环境，并且可以使用它来测试您自己的应用程序和秘密管理。

## 资源

*   [金库](https://www.vaultproject.io/)主页
*   [psecio\vaultlib 包](https://github.com/psecio/vaultlib)
*   [Docker.com](https://www.docker.com/)
*   [码头工复合手册](https://docs.docker.com/compose/)
*   Nginx 主页
*   PHP-FPM