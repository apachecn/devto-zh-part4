# SFTP 到 S3 的备份

> 原文：<https://dev.to/babbarshaer/sftp-to-s3-backup-2e18>

在我们公司，我们有一个简单的 SFTP 服务器，第三方用来为我们提供报告、文件等。为了将信息备份到我们可以开始处理文件的 S3 上，我在`Go`中编写了一个简单的实用程序来实现相同的 [sftp-s3-backup](https://github.com/babbarshaer/sftp-s3-backup) 。

本文的目的是通过一个简单的过程将 SFTP 的文件备份到 S3。

# S3 登录凭证

为了备份到 S3，我们的系统需要能够登录到平台。需要设置以下环境变量，以便程序自动选择它们。这些变量如下:

1.  `AWS_ACCESS_KEY_ID`
2.  `AWS_SECRET_ACCESS_KEY`
3.  `AWS_REGION`
4.  `AWS_SESSION_TOKEN` //如果您启用了双因素验证。

# 简单备份

```
package main

import (
  "log",
  backup "github.com/babbarshaer/sftp-s3-backup"
)

func main() {

  config := backup.Config{
    User:              "user",
    Address:           "address",
    Port:              22,
    PublicKeyLocation: "/home/user/.ssh/id_rsa.pub",
  }

  client := backup.Client(config)
  err = client.Init()
  if err != nil {
    log.Fatalf("Unable to initialize the client: %s", err.Error())
  }

  defer client.Close()

  err := client.Backup(
    "/data/user/uploads",
    "aws.bucket",
    backup.DefaultPathTransformer)

  // If we don't want the default path transformer
  // we can override it with our own implementation.

  if err != nil {
    log.Fatalf("Unable to backup, err: %s", err.Error())
  } 
```

我们将逐步介绍计划的每个部分:

我们首先创建一个`config`对象，它主要包含 sftp 服务器的凭证，我们需要将该服务器的目录备份到 S3。

```
config := backup.Config{
    User:              "user",
    Address:           "address",
    Port:              22,
    PublicKeyLocation: "/home/user/.ssh/id_rsa.pub",
  } 
```

然后，我们通过在 config 中提供来创建一个新的备份`client`对象。我们通过调用`Init()`方法来初始化客户端。

```
client := backup.Client(config)
client.Init() 
```

如果一切顺利，我们应该能够在这一点之后建立 sftp 连接。否则，它将返回一个错误。此时，错误的处理取决于用户。

我们应该永远记住释放客户端连接。

```
defer client.Close() 
```

最后，我们现在可以执行目录备份。该方法将目录中的所有文件备份到 s3 中相应的桶中。我们提供了一个额外的`transformer`变量，它决定了在将对象从 sftp 存储到 s3 上时，需要如何转换路径。

```
directoryToBackup := "/data/user/uploads"
awsBucket := "aws.bucket"
transformer := backup.DefaultPathTransformer

err := client.Backup(
    directoryToBackup,
    awsBucket,
    transformer) 
```

用户可以提供自己的`transformer function`如下:

```
func MyTransformer(path string) string {
  newPath := path
  // computation ...
  return newPath
} 
```

* * *