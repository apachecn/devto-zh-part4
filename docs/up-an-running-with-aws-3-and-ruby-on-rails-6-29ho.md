# 运行 AWS 3 和 Ruby On Rails 6

> 原文：<https://dev.to/ispirett/up-an-running-with-aws-3-and-ruby-on-rails-6-29ho>

部署应用程序时，最好使用第三方服务来托管图像、视频文件等。使用服务器存储会大大增加你每月或每年的服务器成本。此外，一些服务，如 Heroku，不允许你永久保存你的文件。

# 解？使用云存储。

我们将使用主动存储实现 Aws 3 作为 Rails 应用程序的云存储。您还可以实现其他服务，设置非常相似，每个服务都需要自己的 gem 和一些其他的小改动。

# 需要宝石

`gem "aws-sdk-s3", require: false`

## 安装活动记录表

活动记录需要两个表来与您的模型相关联，`active_storage_blobs`和`active_storage_attachments`。

在您的终端中运行这些命令，确保您位于项目目录中。

`rails active_storage:install rails db:migrate`

# 设置您的**配置/存储. ymlfile**

在 storage.yml 文件中，取消 amazon 部分的注释

[![CODE](img/132471eb3aad0d6e43617e14ed608f59.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--npmQpr_n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2AdfrhPCP-TqxRkNtbZXjfIA.png)

# 设置访问密钥和秘密访问密钥

在 rails 5.2 及更高版本中，我们现在可以使用`config/credentials.yml.enc`。这将安全地存储您的加密凭证，并要求`config/master.key`在生产中解密。

在您的终端中，输入`EDITOR="code --wait" rails credentials:edit`
如果您使用另一个编辑器，如`atom, vim, or webstorm`，用您的编辑器名称替换“代码”。

#### 应出现如下窗口:

[![CODE 2](img/26b79712588c476f6a2d1c864d8a1d9a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DYKcxIpU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2AHdQ4qyFbh0-C2qVIKI12oQ.png)

您现在应该取消对这些行的注释，并在`amazon`上从您的`Identity and Access Management (IAM)`中获取您的`access_key and secret_access_key`。

[![CODE3](img/4bcaf37dc8c770fcb32c2336d6ee7c11.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SO2kFyo7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2AxiNc4jlXyVNYo1xSleMRFA.png)

我添加了一个桶密钥，但你不必这样做。您可以在`storage.yml`文件中添加您的存储桶名称。
完成后，保存文件。

# 检查 credentials.yml.enc 文件

[![code](img/a07d54209fa03e60469ee8046540aba9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Yo85mebE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2Ao3kW6ycFefJE5FeIL_pz0w.png)

您可以使用`rails console`进行测试，您应该会得到之前输入的值。

### 现在你的配置文件应该是这样的。

[![Code](img/132471eb3aad0d6e43617e14ed608f59.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--npmQpr_n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2AdfrhPCP-TqxRkNtbZXjfIA.png)

不要忘记使用正确的区域。

# 生产开发设置

在你的`config/environments/production`和`config/environments/development`中，寻找这一行(如下图所示)并将其更改为**":亚马逊"**

[![Code](img/24a40a4a9baa9980335afdf3d2c5f9b8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hMXmIrYI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2A1Fso_PF7S5DxToDb-kkkxQ.png)

你现在应该有能力在开发中测试 amazon #####aws3 了。

# 在生产中设置

### *别忘了把你的改动推送给 Heroku*

去你的 Heroku 账户。
转到您的设置选项卡。
点击**显示配置变量**。
添加新的密钥 **RAILS_MASTER_KEY** 。
检索`yourconfig/master.key`中的任意文本，并将该文本作为 **RAILS_MASTER_KEY** 的值。

### 此时一切都应该运转正常。

[![code](img/2a7494c28b38db8a768b90454be51351.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gA6MgLJr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2AjfdRd1IgxlY6Knpi8F8tTg.gif)

# 结论

我希望这有助于建立一个没有压力的环境。

#### 如果您还没有设置 s3 存储桶，请考虑查看这些链接。

[AWS](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html)
T3】AWS docs