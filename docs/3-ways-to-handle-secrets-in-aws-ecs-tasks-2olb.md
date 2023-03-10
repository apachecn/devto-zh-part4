# 在 AWS ECS 任务中处理机密的 3 种方法

> 原文：<https://dev.to/sagarjauhari/3-ways-to-handle-secrets-in-aws-ecs-tasks-2olb>

无论我们在应用程序安全性上花费多少时间和精力，它总是更少。但是，像秘密管理、密钥轮换和密码过期这样的简单工作流在使我们的应用程序和基础设施抵御明显的攻击媒介方面大有作为。

如果您使用过 AWS 弹性容器服务(ECS) / Fargate，那么您肯定遇到过向运行中的容器传递秘密的问题。这篇文章描述了 3 种方法来将秘密作为环境变量传递给作为 AWS ECS 任务运行的应用程序。

**方法 1** —在 ECS 任务定义中将秘密作为环境变量传递

*   好:需要最少的基础设施
*   不好:ECS 任务定义与秘密紧密结合。如果您更改了机密 Bad 的值，则需要进行新的部署:对 ECS 任务定义具有访问权限的任何人都可见的机密

**方法 2** —使用入口点脚本获取最新机密

使用 docker 入口点脚本，您可以在应用程序启动之前从任何地方获取机密。

*   好:秘密只在运行时对应用程序可见，在此之前不可见。这意味着它们不是 ECS 任务定义的一部分
*   不好:获取机密的逻辑被重复嵌入到不同的应用程序中。因此，虽然秘密(希望)保存在您的基础设施配置中，但是每个应用程序都需要知道如何以及从哪里获取它们。这违反了[干](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)和[单责原则](https://en.wikipedia.org/wiki/Single_responsibility_principle)。

Github 上的许多存储库都使用这种方法。这里还描述了一个以这种方式从 AWS S3 获取机密的例子[。](https://aws.amazon.com/blogs/security/how-to-manage-secrets-for-amazon-ec2-container-service-based-applications-by-using-amazon-s3-and-docker/)

**方法 3** —使用 ECS 任务定义的“valueFrom”属性

这种方法将获取机密的负担放在 AWS 上，应用程序容器可以在应用程序运行之前期待环境变量的存在

*   好:应用程序不需要知道如何获取秘密——它希望它们在运行时作为环境变量可用。
*   好:支持 AWS SSM 参数存储和 AWS 系统管理器(目前)

**我的推荐**

我推荐的方法是使用“valueFrom”属性将 KMS 加密的 AWS SSM 参数直接注入到正在运行的任务中。这样，秘密既不会被登录 AWS 控制台的人看到，也不会以纯文本的形式保存在任何地方。

将添加到 AWS SSM 参数存储中的参数的路径标准化是一个好主意，因为这将有助于限制对这些秘密的访问范围。您设计基础设施的方式肯定会决定您可能使用的路径格式。您决定什么格式并不重要，重要的是标准化“某种格式”,而不是允许每个服务指定自己的路径格式。所以，总的来说，这里是我推荐的 3 个步骤:

**第一步**:决定你的 AWS SSM 参数库的路径格式。例如:

```
/{environment}/{service}/DATABASE_PASSWORD
/{service}/{enviroment}.DATABASE_PASSWORD 
```

**步骤 2** :将您的键值对添加到 AWS SSM 参数存储中，并使用 KMS 密钥对它们进行加密

**步骤 3** :使用‘secrets/value from’属性将 AWS SSM 参数直接注入到正在运行的任务中

这是实现这一点的地形配置。这些文件只包含 secrets 所需的子句—您仍然需要填写其余的细节来实际创建 ECS 任务。
地形文件(部分)

```
data "template_file" "blog" {
  template = "${file("template.json")}"

  vars {
    app_name    = "blog"
    environment = "staging"
    region      = "us-east-1"
    account     = "12341234"
  }
}

resource "aws_ecs_task_definition" "blog" {
  container_definitions = "${data.template_file.blog.rendered}"

  family             = "blog"
  task_role_arn      = "${var.iam_role_arn}"
  execution_role_arn = "${var.iam_role_arn}"
} 
```

模板文件:

```
[  {  "secrets":  [  {  "name":  "DATABASE_PASSWORD",  "valueFrom":  "arn:aws:ssm:${region}:${account}:parameter/${environment}/${appName}/DATABASE_PASSWORD"  }  ]  }  ] 
```

> 注意:如果您在 ECS 任务定义中使用“secrets/valueFrom ”,您还需要给它一个“serviceRoleArn ”,它有权读取该范围所需的 SSM 参数。

**补充阅读**

*   [AWS 文档 Amazon ECS 开发人员指南指定敏感数据的 Amazon ECS 任务定义](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/specifying-sensitive-data.html)
*   [AWS 文档亚马逊 ECS 开发者指南亚马逊 ECS 任务定义任务定义参数](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_definition_parameters.html)
*   [使用任务的参数存储和 IAM 角色管理 Amazon ECS 应用的秘密](https://aws.amazon.com/blogs/compute/managing-secrets-for-amazon-ecs-applications-using-parameter-store-and-iam-roles-for-tasks/)
*   [如何使用亚马逊 S3 和 Docker 为基于亚马逊 EC2 容器服务的应用管理机密](https://aws.amazon.com/blogs/security/how-to-manage-secrets-for-amazon-ec2-container-service-based-applications-by-using-amazon-s3-and-docker/)
*   【AWS ECS 内的机密管理
*   [ECS 秘笈做对了](https://hackernoon.com/ecs-secrets-done-right-9e094cfa6200)

*本帖最初发表在[媒体](https://medium.com/faun/3-ways-to-handle-secrets-in-aws-ecs-tasks-c3f4c5d688f9)T3 上*