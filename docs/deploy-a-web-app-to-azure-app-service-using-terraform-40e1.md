# 使用 Terraform 将 web 应用部署到 Azure 应用服务

> 原文：<https://dev.to/deepu105/deploy-a-web-app-to-azure-app-service-using-terraform-40e1>

*最初发布于[deepu . tech](https://deepu.tech/deploy-a-web-app-to-azure-app-service-using-terraform/)T3。*

将 Java web 应用程序部署到 Azure 很容易，并且已经被很多人尝试、测试和解释了很多次。我的朋友朱利安·杜布瓦(Julien Dubois)有一个很好的系列文章[在这里](https://dev.to/azure/creating-a-spring-boot-and-angular-application-for-azure-1-7-2mb8)。Azure 非常容易使用其[应用服务](https://azure.microsoft.com/en-gb/services/app-service/)，因为它提供了许多部署 web 应用的不同方式。

如果您是一名现代的全栈 Java 开发人员，那么您很有可能将应用程序部署为 Docker 映像。因此，今天让我们看看如何使用 Docker 和 Terraform，本着基础设施即代码的真正精神，将 Java web 应用程序部署到 Azure App Service。这种方法对于任何构建为 docker 映像的 web 应用程序来说都是一样的，不一定只局限于 Java。

要尝试这一点，你需要安装 [Java](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 、 [NodeJS](https://nodejs.org/en/download/) 、 [Terraform](https://www.terraform.io/) 、 [Docker](https://docs.docker.com/install/) 和 [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) 。如果需要，请按照链接进行安装。

作为 JHipster(一个方便的开发平台，用于生成、开发和部署 Spring Boot+Angular/React/Vue Web 应用程序和 Spring 微服务)的主要开发人员之一，我在这里使用 JHipster web 应用程序作为例子。所以让我们开始吧。

让我们使用 JHipster 构建一个非常简单的 web 应用程序。我们将使用 [JDL](https://www.jhipster.tech/jdl/) 特性来搭建我们的应用程序。

我们将在我们的应用程序中使用下面的 JDL。将它保存到您想要创建应用程序的目录中的一个名为`app.jdl`的文件中。

```
application {
    config {
        baseName helloJHipster,
        applicationType monolith,
        packageName tech.jhipster.demo,
        authenticationType jwt,
        buildTool gradle,
        clientFramework react,
        databaseType sql,
        prodDatabaseType mysql,
        languages [en, nl]
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们使用 JHipster 来搭建这个平台。打开您最喜欢的控制台/终端，在您保存上述 JDL 文件的目录中运行下面的命令，确保它是一个空目录。

```
$ npx generator-jhipster import-jdl app.jdl 
```

Enter fullscreen mode Exit fullscreen mode

如果你已经安装了 JHipster [和](https://www.jhipster.tech/installation/)，你可以运行

```
$ jhipster import-jdl app.jdl 
```

Enter fullscreen mode Exit fullscreen mode

这将搭建应用程序并安装所需的客户端依赖项。这可能需要几分钟(NPM！)所以也许是时候喝咖啡了。

搭建完成后，您可以通过在同一终端上运行`./gradlew`来查看应用程序的运行情况。您可以参考生成的`Readme.md`以获得更多关于应用程序的说明。

现在让我们继续这篇文章的重点，用 Terraform 将它部署到 Azure 应用服务。让我们首先为我们的应用程序构建并发布 docker 图像。

JHipster 方便地提供了构建 docker 映像所需的一切。让我们使用提供的 docker 集成，使用 [JIB](https://github.com/GoogleContainerTools/jib) 来构建图像。运行下面的 Gradle 命令。

```
$ ./gradlew bootJar -Pprod jibDockerBuild 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们标记并将其推送到我们的 docker 注册表，确保您已经登录到 docker 并运行这些命令。使用您自己的 docker hub 帐户名称。

```
$ docker tag hellojhipster:latest deepu105/hellojhipster:latest
$ docker push deepu105/hellojhipster:latest 
```

Enter fullscreen mode Exit fullscreen mode

如果你愿意，也可以推送到 Azure Container registry，而不是 Docker Hub。

现在我们的应用程序和 Docker 映像已经准备好了，让我们为 App Service 和 MySQL 数据库准备 Terraform 基础设施。关于将 JHipster web 应用程序部署到 Azure 的其他方式，请查看此。

首先，为我们的 terraform 文件创建一个文件夹。我们把这个文件夹命名为`terraform`。

现在在这个文件夹中创建三个名为`main.tf`、`outputs.tf`和`variables.tf`的文件。

让我们定义我们将使用的变量。将下面的内容保存在`variables.tf`中。

```
variable "prefix" {
  description = "The prefix used for all resources in this example"
  default     = "xl"
}

variable "location" {
  description = "The Azure location where all resources in this example should be created"
}

variable "subscription_id" {
  description = "Azure Subscription ID to be used for billing"
}

variable "my_sql_master_password" {
  description = "MySql master password"
}

variable "docker_image" {
  description = "Docker image name"
}

variable "docker_image_tag" {
  description = "Docker image tag"
} 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们定义我们的`main.tf`

首先，让我们为 Azure 资源管理器添加一个配置，并创建一个 Azure 资源组来保存我们的资源。

```
provider "azurerm" {
  version         = "=1.24.0"
  subscription_id = "${var.subscription_id}"
}

resource "azurerm_resource_group" "main" {
  name     = "${var.prefix}-resources"
  location = "${var.location}"
} 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们添加配置来创建一个 [MySQL 数据库服务器](https://www.terraform.io/docs/providers/azurerm/r/mysql_server.html)以及所需的防火墙规则，以便让应用服务访问数据库。如果您想从您的机器添加本地访问，也为您的 IP 添加防火墙规则块。

```
# This creates a MySQL server
resource "azurerm_mysql_server" "main" {
  name                = "${var.prefix}-mysql-server"
  location            = "${azurerm_resource_group.main.location}"
  resource_group_name = "${azurerm_resource_group.main.name}"

  sku {
    name     = "B_Gen5_2"
    capacity = 2
    tier     = "Basic"
    family   = "Gen5"
  }

  storage_profile {
    storage_mb            = 5120
    backup_retention_days = 7
    geo_redundant_backup  = "Disabled"
  }

  administrator_login          = "mysqladminun"
  administrator_login_password = "${var.my_sql_master_password}"
  version                      = "5.7"
  ssl_enforcement              = "Disabled"
}

# This is the database that our application will use
resource "azurerm_mysql_database" "main" {
  name                = "${var.prefix}_mysql_db"
  resource_group_name = "${azurerm_resource_group.main.name}"
  server_name         = "${azurerm_mysql_server.main.name}"
  charset             = "utf8"
  collation           = "utf8_unicode_ci"
}

# This rule is to enable the 'Allow access to Azure services' checkbox
resource "azurerm_mysql_firewall_rule" "main" {
  name                = "${var.prefix}-mysql-firewall"
  resource_group_name = "${azurerm_resource_group.main.name}"
  server_name         = "${azurerm_mysql_server.main.name}"
  start_ip_address    = "0.0.0.0"
  end_ip_address      = "0.0.0.0"
} 
```

Enter fullscreen mode Exit fullscreen mode

这将创建一个 MySQL 服务器，我们的应用程序在服务器上的数据库，并允许从应用程序服务访问。

现在让我们配置[应用服务](https://www.terraform.io/docs/providers/azurerm/r/app_service.html)本身以及服务计划。

```
# This creates the plan that the service use
resource "azurerm_app_service_plan" "main" {
  name                = "${var.prefix}-asp"
  location            = "${azurerm_resource_group.main.location}"
  resource_group_name = "${azurerm_resource_group.main.name}"
  kind                = "Linux"
  reserved            = true

  sku {
    tier = "Standard"
    size = "S1"
  }
}

# This creates the service definition
resource "azurerm_app_service" "main" {
  name                = "${var.prefix}-appservice"
  location            = "${azurerm_resource_group.main.location}"
  resource_group_name = "${azurerm_resource_group.main.name}"
  app_service_plan_id = "${azurerm_app_service_plan.main.id}"

  site_config {
    app_command_line = ""
    linux_fx_version = "DOCKER|${var.docker_image}:${var.docker_image_tag}"
    always_on        = true
  }

  app_settings = {
    "WEBSITES_ENABLE_APP_SERVICE_STORAGE" = "false"
    "DOCKER_REGISTRY_SERVER_URL"          = "https://index.docker.io"

    # These are app specific environment variables
    "SPRING_PROFILES_ACTIVE"     = "prod,swagger"
    "SPRING_DATASOURCE_URL"      = "jdbc:mysql://${azurerm_mysql_server.main.fqdn}:3306/${azurerm_mysql_database.main.name}?useUnicode=true&characterEncoding=utf8&useSSL=false&useLegacyDatetimeCode=false&serverTimezone=UTC"
    "SPRING_DATASOURCE_USERNAME" = "${azurerm_mysql_server.main.administrator_login}@${azurerm_mysql_server.main.name}"
    "SPRING_DATASOURCE_PASSWORD" = "${var.my_sql_master_password}"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个配置中，在`site_config`下，我们使用`linux_fx_version`来声明我们的 docker 映像，并将`always_on`设置为 true，这样应用程序在一段时间不活动时不会关闭。

在`app_settings`部分，我们需要使用标志`WEBSITES_ENABLE_APP_SERVICE_STORAGE`禁用存储，并指定`DOCKER_REGISTRY_SERVER_URL`。其他一切都是特定于我们的应用程序。传递给 MySQL 连接 URL 的标志非常重要。

现在我们的`main.tf`已经准备好了，让我们定义一些方便的输出属性。在`outputs.tf`文件中添加下面的

```
output "app_service_name" {
  value = "${azurerm_app_service.main.name}"
}

output "app_service_default_hostname" {
  value = "https://${azurerm_app_service.main.default_site_hostname}"
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们准备好摇滚了！让我们部署应用程序。确保您已经设置了 Azure CLI，并使用`az login`登录了。现在，在终端/控制台中，导航到我们创建的`terraform`文件夹并执行这些命令。请相应地更改`prefix`、`location`、&、`docker_image`的值。

```
$ terraform init

$ terraform apply \
-var prefix=myAwesomeApp \
-var location=northeurope \
-var docker_image=deepu105/hellojhipster \
-var docker_image_tag=latest 
```

Enter fullscreen mode Exit fullscreen mode

这将提示您输入 MySQL 服务器的主密码和您的 Azure 订阅 ID(您可以从 Azure 门户网站或通过运行`az account list`-`id`字段是订阅 ID)找到它。一旦您提供了值并确认，Terraform 将开始工作并开始创建资源。这可能需要一段时间，因为我们正在配置数据库服务器。等待或者去喝第二杯咖啡；)

一旦部署完成，Terraform 将打印出包含`app_service_default_hostname`的输出。复制 URL 并在您最喜欢的浏览器中打开它。第一次可能需要一段时间，因为应用程序将只在第一次请求时启动(冷启动)。

[![](img/46beeb11ff1bc3eb2028c6c852800445.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QwwtKxey--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t5kcj7r3r9w6bxghe350.png)

我希望你觉得这很有用。这是我在 [dev.to](https://dev.to/deepu105) 的第一篇文章，我希望我的博客能尽快从 [Medium](https://medium.com/@deepu105) 迁移到 [dev.to](https://dev.to/deepu105) 。

如果你喜欢这篇文章，请留下赞或评论。

你可以在 [Twitter](https://twitter.com/deepu105) 和 [LinkedIn](https://www.linkedin.com/in/deepu05/) 上关注我。

我的其他相关帖子:

*   [使用 JHipster 领域语言在 30 分钟内创建完整的微服务堆栈](https://deepu.tech/create-full-microservice-stack-using-j-hipster-domain-language-under-30-minutes)
*   [在 Azure Kubernetes 服务(AKS)上部署 JHipster 微服务](https://deepu.tech/deploying-jhipster-microservices-on-azure-kubernetes-service-aks)
*   [如何在 Kubernetes 上设置带有 Istio 服务网格的 JHipster 微服务](https://deepu.tech/jhipster-microservices-with-istio-service-mesh-on-kubernetes)
*   [以 XebiaLabs —又名 DevOps 为代码持续交付微服务](https://medium.com/xebialabs/continuous-delivery-of-microservices-with-xebialabs-a-k-a-devops-as-code-b5f0c3b2b1c8)