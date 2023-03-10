# Google 日历 API 集成变得简单

> 原文：<https://dev.to/megazear7/google-calendar-api-integration-made-easy-2a68>

最近，在尝试为一个网站创建活动列表时，我很难集成 Google Calendar API。虽然现在回想起来，大部分都应该很容易，但是需要一些复杂的配置。我想分享我将一个网站与谷歌日历 API 集成的步骤。我们将使用 JWT 认证，这是连接到 Google API 最安全、最灵活的方式。请记住，这种类型的连接是连接到您管理的 Google，而不是用户的 Google 帐户。

## 启用日历 API

首先，我们需要设置 Google API，并收集连接到它所需的各种连接信息。首先，只需启用日历 API。

1.  第一步是转到 [Google API 控制台](https://console.developers.google.com/apis/dashboard)，选择一个现有项目或创建一个新项目。
2.  然后选择“启用 API 和服务”按钮
3.  在搜索框中键入“日历”
4.  点击“谷歌日历应用编程接口”
5.  单击“启用”

## 设置 JWT 权限

接下来，我们需要创建一个 JSON Web 令牌，通常称为“JWT”。这将为我们提供在安全的上下文中以编程方式访问各种 Google API 所需的信息。

1.  转到[谷歌 API 控制台](https://console.developers.google.com/apis/dashboard)
2.  从左侧导航栏中选择“凭据”
3.  单击“创建凭据”按钮
4.  选择“服务帐户密钥”
5.  这会将一个 JSON 文件下载到您的计算机上。在这篇博文中，我们将它称为 JWT JSON 文件。

## 检索谷歌项目编号

现在我们需要检索 Google API 需要的 Google 项目编号。

1.  转到[谷歌 API 控制台](https://console.developers.google.com/apis/dashboard) c
2.  在屏幕的右上角，单击您的个人资料图片旁边的三点菜单选项。
3.  单击“项目设置”
4.  复制“项目编号”并粘贴到某处以便安全保管。我们将在这篇博文的代码示例中引用这一点

## 设置谷歌日历

最后，我们需要配置 Google 日历并检索 ID。这是一个棘手的步骤，因为您必须让 JWT 令牌访问您的日历，如下面的步骤 5-7 所述。

1.  前往[谷歌日历](https://calendar.google.com/calendar/r)
2.  在左侧，将鼠标悬停在要列出事件的日历上，然后单击三点菜单图标
3.  单击“设置和共享”
4.  向下滚动并复制“日历 ID ”,将其粘贴到某个地方以便安全保存。我们将在这篇博文的代码示例中引用这一点。
5.  打开 JWT JSON 文件，复制客户邮件。
6.  转到日历的“与特定人共享”部分，然后单击“添加联系人”
7.  粘贴客户端电子邮件并提交对话框

## 设置示例快递项目

现在让我们使用 Node 创建一个示例项目。带有 Express 服务器的 JS。为您的项目创建一个目录，并运行以下命令:

```
npm init
npm install express --save
npm install googleapis --save 
```

Enter fullscreen mode Exit fullscreen mode

## 创建您的快递服务器

最后，我们需要编写代码，启动一个 express 服务器来监听请求，用上面检索到的连接细节联系 Google Calendar API，然后回复结果。请注意，在文件开头定义的常量需要根据我们之前检索的相应值进行更新。和值都来自 JWT JSON 文件。

/index.js

```
const express = require('express');
const { google } = require('googleapis');

const app = express();
const port = 3000;

const SCOPES = 'https://www.googleapis.com/auth/calendar.readonly';
const GOOGLE_PRIVATE_KEY="<private-key>"
const GOOGLE_CLIENT_EMAIL = "<client-email>"
const GOOGLE_PROJECT_NUMBER = "<project-number>"
const GOOGLE_CALENDAR_ID = "<calendar-id>"

app.get('/', (req, res) => {
  const jwtClient = new google.auth.JWT(
    GOOGLE_CLIENT_EMAIL,
    null,
    GOOGLE_PRIVATE_KEY,
    SCOPES
  );

  const calendar = google.calendar({
    version: 'v3',
    project: GOOGLE_PROJECT_NUMBER,
    auth: jwtClient
  });

  calendar.events.list({
    calendarId: GOOGLE_CALENDAR_ID,
    timeMin: (new Date()).toISOString(),
    maxResults: 10,
    singleEvents: true,
    orderBy: 'startTime',
  }, (error, result) => {
    if (error) {
      res.send(JSON.stringify({ error: error }));
    } else {
      if (result.data.items.length) {
        res.send(JSON.stringify({ events: result.data.items }));
      } else {
        res.send(JSON.stringify({ message: 'No upcoming events found.' }));
      }
    }
  });
});

app.listen(port, () => console.log(`Example app listening on port ${port}!`)); 
```

Enter fullscreen mode Exit fullscreen mode

现在用`node index.js`运行你的服务器，并转到 [localhost:3000](http://localhost:3000/) 查看结果。记住将私钥和其他常量移到环境变量中，而不是提交给源代码控制。您应该会看到添加到 Google 日历中的下 10 个事件。这可以用于将 Google 日历与网站上即将到来的事件功能相集成，或者可以作为需要 Google 日历集成的其他功能的起点。最后，这里展示的 JWT 认证可以用于各种各样的 Google APIs。