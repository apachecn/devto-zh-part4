# 使用 Netlify 云功能将数据备份到 Google Drive

> 原文：<https://dev.to/mornir/use-netlify-cloud-function-to-back-up-data-to-google-drive-49ep>

# 简介

在这篇博文中，我们将编写一个云函数，它将在每次 CMS 中发布新内容时创建一个备份。这些数据将被存储在一个 GZIP 文件中，然后上传到 Google Drive。

后端(CMS)由 Sanity 管理和托管。他们的 CMS，Sanity studio，是一个开源的 React 应用程序，你应该去看看。他们提供一个[服务](https://www.sanity.io/create),用你最喜欢的前端框架快速启动一个新的健全项目。

由于健全是托管服务，一切都是为你管理，你的数据是安全的。Sanity 公司的人有他们自己的备份程序，但是你不能访问备份文件。它们是在数据丢失的情况下使用的。如果它发生在你这边(例如，意外删除数据库)，你最好准备好自己的备份。(请注意，在 studio 中，您总是可以将文档恢复到以前的版本，并撤消删除操作。因此，数据丢失的风险非常低。)

至于 Netlify，你可能已经知道了。这是一个了不起的平台，有很多有用的服务，比如云功能，让你可以轻松地执行服务器端代码。他们最近推出了 *Netlify Dev* ，可以让你轻松地在本地测试你的云功能。非常适合我们的使用案例！所以让我们开始吧！

# 设置 Google Drive API

向谷歌认证比我想象的要难，这就是为什么我决定为它专门写一篇文章:

[![mornir](img/b1537c4e83b5e2de20887997eafda6cd.png)](/mornir) [## 创建一个服务帐户以向 Google 进行身份验证

### 杰罗姆波特 8 月 25 日 193 分钟阅读

#javascript #webdev #tutorial #node](/mornir/create-a-service-account-to-authenticate-with-google-5b1k)

现在，您应该有一个 JSON 文件，其中包含您的 Drive API 凭证和共享文件夹的 ID。

**关于安装 npm 包的注意事项:**
你应该把你的云函数的所有依赖项都安装在你的 main package.json 里面，在 Netlify 的官方例子中，每个云函数都有自己的 package.json，但是我注意到 Netlify 有时候会无法安装那里指定的依赖项。

# 设置网络寿命

我假设您的前端托管在 Netlify。首先使用 Netlify 仪表板在两个 Netlify 环境变量(例如 CREDENTIALS 和 FOLDER_ID)中添加共享文件夹 ID 和 JSON 文件的内容。由于您的驱动 API 凭证现在是一个字符串，我们将使用`JSON.parse(process.env.CREDENTIALS)`读取它。

然后将这一行添加到您的 netlify.toml 中的 build:

```
[build]
  functions = "functions" 
```

Enter fullscreen mode Exit fullscreen mode

这一行告诉 Netlify 你在哪个文件夹中保存你的云函数。创建这个文件夹并创建一个 JS 文件。这个文件将是我们的无服务器功能。通常文件名并不重要，但是在我们的例子中，重要的是命名为`deploy-succeeded.js`。当部署成功时，将自动触发具有此确切名称的云功能。你可以在这里找到其他触发[。](https://www.netlify.com/docs/functions/#event-triggered-functions)

如果还没有完成，现在全局安装`netlify-cli`并在你的项目中启动`netlify dev`。它应该会自动检测所使用的框架类型(Nuxt、Next 等。).如果没有，请确保您没有更改默认端口。(例如，3000 是 Nuxt 的默认端口)。

现在如果访问网址`localhost:8888/.netlify/functions/deploy-succeeded`，可以手动触发该功能。最好的事情是您可以在`process.env`下访问您的环境变量！

# 导出数据

npm 包`@sanity/client`使得导出过程极其容易。将其添加到主 package.json 文件中。

```
 const DATASET = process.env.DATASET

    const sanityClient = sanity({
      projectId: process.env.PROJECT_ID,
      dataset: DATASET,
      token: process.env.SANITY_TOKEN,
      useCdn: false,
    })

    exportDataset({
        // Instance of @sanity/client configured to your project ID and dataset
        client: sanityClient,

        // Name of dataset to export
        dataset: DATASET,

        // Path to write zip-file to
        outputPath: path.join('/tmp', `${DATASET}.tar.gz`),

        // Whether or not to export assets
        assets: false,

        // Exports documents only
        raw: true,

        // Whether or not to export drafts
        drafts: false,
     }) 
```

Enter fullscreen mode Exit fullscreen mode

注意事项:

*   所有环境变量都保存在 Netlify 仪表板中。
*   我们不备份资产(图像、视频等)。)和草稿。如果您想要备份资产，您需要使用与下面描述的[不同的](https://developers.google.com/drive/api/v3/manage-uploads#resumable)上传方法。还要记住，Google Drive 空闲层的容量限制为 15 GB。
*   路径是一个特殊的位置，可以让你临时存储文件。

# 上传数据转储到 Google Drive

现在我们可以引入 Google Drive API:

```
 const FOLDER_ID = process.env.FOLDER_ID

      const client = await google.auth.getClient({
        credentials: JSON.parse(process.env.CREDENTIALS),
        scopes: 'https://www.googleapis.com/auth/drive.file',
      })

      const drive = google.drive({
        version: 'v3',
        auth: client,
      })

      await drive.files.create({
        requestBody: {
          name: `${DATASET}.tar.gz`,
          mimeType: 'application/gzip',
          parents: [FOLDER_ID],
        },
        media: {
          mimeType: 'application/gzip',
          body: fs.createReadStream(path.join('/tmp', `${DATASET}.tar.gz`)),
        },
      })

      // Delete oldest if more than 5 files

      // Get list of backup files inside folder with specified id
      const res = await drive.files.list({
        fields: 'files(id, parents, createdTime)',
        q: `'${FOLDER_ID}' in parents`,
        orderBy: 'createdTime',
      })

      // Keep max. 5 backups
      if (res.data.files.length >= 5) {
        // Delete oldest backup
        drive.files.delete({ fileId: res.data.files[0].id })
      } 
```

Enter fullscreen mode Exit fullscreen mode

我认为代码是不言自明的。我喜欢`async/await`语法让代码更具可读性。

我们通过从`/temp`位置读取来创建上传请求，然后我们通过获取共享文件夹中所有文件的列表并检查其长度是否大于或等于 5 来确保我们保留的备份文件不超过 5 个。如果我们有超过 5 个文件，我们删除最后一个文件。

# Netlify 处理程序方法

每个部署为云函数的 JavaScript 文件都必须导出一个处理程序。在这个处理程序中，您应该调用回调方法，传递带有响应对象的`null`(如果没有发生错误)或被捕获的错误。在下面的代码片段中，我们假设我们有一个名为 *backup* 的函数，它包含我们的备份逻辑。

```
 exports.handler = function(event, context, callback) {
      backup()
        .then(() => {
          callback(null, {
            statusCode: 200,
            body: 'Backup completed successfully!',
          })
        })
        .catch(e => {
          callback(e)
        })
    } 
```

Enter fullscreen mode Exit fullscreen mode

消息“备份成功完成！”否则，该错误将会显示在控制台的 Netlify dashboard 的 functions 选项卡下。

# 结论与告诫

我在生产中为一两个人管理的非常小的网站使用这个备份功能。这足以满足我的需求，但显然有一些限制:

*   资产和草稿不会被保存。
*   如果网站在短时间内部署了五次，则最早的备份将是最近的。

我认为这份初稿是一个很好的起点。我们还可以通过 cron 作业触发这个云功能。有很多种可能。

下面是我的一个个人网站使用的完整函数:[https://github . com/mornir/copy work-portfolio/blob/master/functions/deploy-succeeded . js](https://github.com/mornir/copywork-portfolio/blob/master/functions/deploy-succeeded.js)