# 玩 Mattermost

> 原文：<https://dev.to/zex/play-with-mattermost-3k16>

Mattermost 提供了一系列 API 来与服务器进行交互。

如果我们有新订单，让我们创建一个每天发布 Mattermost 消息的服务。

新订单消息包含一条明文消息和一个 Excel 文件，该文件也是由服务生成的。

该消息包含表情符号，只需将它们作为明文包含即可。

我们今天有新订单！！👏👏

```
msg := "We have new orders today!! :clap::clap:" 
```

首先上传文件，以便帖子可以通过添加文件 ID 来包含它。`data`是文件内容。

```
frsp, rsp := cli.UploadFile(data, chann_id, fd.Name()) 
```

太好了！现在文件 id 在`frsp`中，这是一个`FileUploadResponse`。提取它们并保存在`file_ids`。

```
 post, rsp := cli.CreatePost(&mm.Post{
    FileIds: file_ids,
    ChannelId: chann_id,
    Message: msg,
  }) 
```

然后我们在频道上有一个新的帖子，标题是`chann_id`，该频道的成员可以看到它。:D

## 亦见

*   [Mattermost Github](https://github.com/mattermost/mattermost-server)
*   [Mattermost](https://mattermost.com)
*   [机器人应用编程接口](https://api.mattermost.com/#tag/bots)