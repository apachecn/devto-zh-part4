# 使用 GitHub 操作发布您的云跑步应用

> 原文：<https://dev.to/brunosabot/publish-your-cloud-run-app-with-github-actions-ab5>

艾伦·尼格伦在 [Unsplash](https://unsplash.com) 上的照片。

谷歌最近宣布了[云运行](https://cloud.google.com/run/)，这是一个新的[谷歌云平台](https://cloud.google.com/gcp/?utm_source=google&utm_medium=cpc&utm_campaign=na-US-all-en-dr-bkws-all-all-trial-p-dr-1007179&utm_content=text-ad-lpsitelinkCCexp2-any-DEV_c-CRE_113120493247-ADGP_Hybrid+%7C+AW+SEM+%7C+BKWS+%7C+US+%7C+en+%7C+Multi+~+Cloud+Platform-KWID_43700011014879364-kwd-10876442192&utm_term=KW_cloud%20platform-ST_cloud+platform&gclid=CjwKCAjw2qHsBRAGEiwAMbPoDG8AYhrx-uAWd-_A5PQbzTIHw7LCFPa1E54xXPqJj3nMh1K3Wt55ChoCNG4QAvD_BwE) (GCP)功能，可以快速轻松地部署你的 [Docker](https://www.docker.com/) 应用。本指南将解释如何使用 GitHub 提供的新的持续集成/持续交付系统构建和部署一个简单的静态应用程序: [Actions](https://github.com/features/actions) 。

为了让这个项目活起来，我们将使用以下工具:

*   谷歌云运行，执行我们的 Docker 容器
*   [Google Cloud Container Registry](https://cloud.google.com/container-registry/)存储我们的 Docker 图片
*   管理持续部署的 GitHub 操作
*   GitHub 来存储我们项目的源代码。

### 配置 GCP 项目

#### 服务账户创建

第一步是创建一个*服务帐户*，它将允许我们从 GitHub actions 进行连接。为此，你可以点击 GCP 界面中“IAM &管理/服务账户”菜单中的“创建服务账户”按钮。现在，在“服务帐户名称”字段中填入值“GitHub-actions”，在“服务帐户描述”字段中填入值“GitHub Actions 用来连接 GCP 的帐户”

此时，保留字段*服务账户 ID* 中生成的值。这是稍后将使用的服务帐户用户名。看起来应该是`github-actions@key-partition-000000.iam.gserviceaccount.com`。

点击“创建”按钮，进入角色配置阶段。我们需要两个角色:

点击“创建”按钮，进入角色配置阶段。我们需要两个角色:

*   **云运行管理员**，该角色允许我们创建新的云运行部署；
*   **存储管理员**，该角色允许我们将 Docker 图像上传到 GCP 的集装箱登记处。
*   **服务账户用户**，允许服务账户充当用户的角色。

“继续”按钮将我们带到第三步，也是最后一步:创建 JSON 密钥。为此，只需点击“创建密钥”按钮，选择“JSON”>“创建”将下载一个文件，称为`key-partition-000000–0123456789ab.json`。留着吧，我们以后会用的。

#### API 激活

现在我们有了一个拥有适当权限的用户，我们需要激活我们需要的两个服务。

要启用容器注册表，选择“容器注册表”(显而易见，对吗？)菜单，点击“启用容器注册 API”按钮…我们就完成了。

要启用云运行，选择“云运行”(再明显不过了，对吧？)菜单，点击“开始使用云运行”再说一次，我们结束了。

### 项目创建

为了启动这个项目，让我们创建一个 GitHub 存储库并添加几个文件:

*   一个简单的欢迎页面；
*   一个简单的错误`<h1>Server error</h1>` HTML 页面；
*   将作为欢迎文件的`nginx/default.conf`文件
*   A `Dockerfile`构建 Docker 图像；
*   一个用于配置持续部署的`.github/workflows/googlecloudrun.yml`文件。

#### `nginx`配置