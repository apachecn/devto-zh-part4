# cURL，云，20GB 的文件和我！

> 原文：<https://dev.to/urishaked/curl-the-cloud-20gb-files-and-i-7g9>

快速提示如何使用 cURL 在不同的云服务之间传输海量文件！

在过去的几年里，我一直在共同组织几个会议，包括 [JavaScript Israel](https://www.meetup.com/JavaScript-Israel/) 和[物联网制造商 Israel](https://www.meetup.com/IoT-Makers-Israel/) 。我坚信应该把所有的演讲都录下来，然后在 YouTube/Vimeo 上分享。

出于某种原因，视频人员有时喜欢使用随机文件共享服务(如 sendgb.com、filemail.com 等)来共享原始视频文件。这些文件通常有几千兆字节大，这些服务只会保留它们几天。

由于我的家庭上传速度不是很快，我一直在寻找一种快速的解决方案，使我能够将这些文件转移到不同的位置，如谷歌云存储或 Vimeo，而不必先将它们下载到我的机器上，然后再重新上传到他们的新家。

经过一番摆弄，我发现了一个非常简单的窍门:

1.  我在我的机器上进入 Chrome 的文件共享服务网站，打开开发者工具。然后我点击“下载”。
2.  我查看了 Chrome 开发者工具的“网络”标签，找到了我的下载请求触发的特定行。我右击它，选择“复制”和“复制为卷曲(巴什)”。复制的命令包括所有相关的 cookies、头和任何提交的表单数据，使得在不同的机器上运行相同的 HTTP 请求并获得相同的文件变得容易。
3.  最后，我通过 SSH 连接到我的一台云机器，粘贴我刚才复制的 cURL 命令，并将其输出通过管道传输到第二个命令，该命令将文件上传到所需的服务。请看下面的一些例子。

[![Copy as cURL(bash)](img/fe1ad9bf0c79fc83533230c0a314e346.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hT7jAeGj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qv854bff82978ksdmlur.png)

## 将文件复制到 Google 云存储或 AWS

为了将文件复制到 GCS，我从任何 Google Cloud VM 使用`gsutil`命令(VM 需要对 GCS 具有写权限)，或者对于较小的文件(< 3GB)，我只需启动[云外壳](https://cloud.google.com/shell/)。结果看起来像这样:

```
curl ... | gsutil cp - gs://bucket-name/filename.mp4 
```

其中`curl ...`是你从 Chrome Devtools 复制的命令，`bucket-name`是你要将文件复制到的目标 GCS bucket，`filename.mp4`是你要调用的文件。

如果您在 AWS 上工作，您可以使用`aws s3`命令来获得类似的结果:

```
curl ... | aws s3 cp - s3://bucket-name/filename.mp4 
```

## 直接上传文件到 Vimeo

我最近发现 Vimeo [在其付费计划上支持 FTP 上传](https://vimeo.zendesk.com/hc/en-us/articles/229504987-Uploading-via-FTP)。因此，我使用类似的方法，但是这次我将`curl`的输出通过管道传输到另一个通过 FTP 上传的`curl`进程:

```
curl ... | curl -T - ftp://user:PASS@ftp-3.cloud.vimeo.com/video.mp4 
```

您可以使用这种方法将文件上传到任何 ftp 服务器——只需将`user`、`PASS`和`ftp-3.cloud.vimeo.com`替换为您的 FTP 帐户的相关值。

## 上传到其他地方——Google Drive、Dropbox 等。

您可以轻松扩展这种方法，并将文件上传到您喜欢的云提供商。例如，对于 Google Drive，你可以使用[g Drive CLI](https://github.com/gdrive-org/gdrive#downloads)并将 cURL 输出传送到`gdrive upload - <path>`。

对于 dropbox，你可以按照这个 StackOverflow 回答中的步骤[来创建一个 cURL 命令，它将给定的文件上传到 Dropbox。您只需要将`--data-binary @matrices.txt`更改为`--data-binary @-`，这样命令就可以从 stdin 获得它的输入，也就是您通过管道传递给它的其他`curl`命令的输出。](https://stackoverflow.com/a/42120964/830623)

## 总结

这里展示的技巧非常简单——多亏了 Chrome 的 DevTools，你可以很容易地将浏览器发出的任何网络请求转换成 cURL 命令，你可以在任何地方运行，在大多数情况下都会产生相同的结果。我利用这种方法是为了在云上使用 Linux 命令传输大文件，但是我相信您可以找到更有创意的用例。当你看到的时候，请与我分享:-)