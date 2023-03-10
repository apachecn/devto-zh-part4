# 带有 AWS Lambda 和节点的 S3 上的 Zip 文件

> 原文：<https://dev.to/lineup-ninja/zip-files-on-s3-with-aws-lambda-and-node-1nm1>

> 这篇文章于 2022 年 9 月 20 日更新，以提高大量文件的可靠性。
> 
> *   更新流处理，以便只有当文件准备好由 Zip 存档程序处理时，流才向 S3 开放。这修复了处理大量文件时可能出现的超时问题。
> *   使用 S3 保持活动状态并限制连接的套接字。

想要将 S3 上的文件打包成一个 Zip 文件，以便用户在一个包中下载多个文件，这种要求并不少见。也许有一天 AWS 自己提供这种功能已经足够普遍了。在那之前，你可以写一个简短的脚本来完成它。

如果您想在 AWS Lambda 这样的无服务器环境中提供这个服务，那么您有两个主要的约束来定义您可以采用的方法。

1 - /tmp 只有 512Mb。你的第一个想法可能是从 S3 下载文件，压缩它们，上传结果。这将很好地工作，直到您用临时文件填满/tmp！

2 -内存限制为 3GB。您可以将临时文件存储在堆上，但是您仍然被限制为 3GB。即使在常规的服务器环境中，您也不会希望一个简单的 zip 函数占用 3GB 的 RAM！

那么你能做什么呢？答案是将数据从 S3 通过一个归档器传输回 S3。

幸运的是[这个堆栈溢出帖子](https://stackoverflow.com/a/50397276/8296409)和它的评论指出了方向，这个帖子基本上是它的翻版！

下面的代码是类型脚本，但是 Javascript 只是删除了类型。

从你需要的进口开始

```
import * as Archiver from 'archiver';
import * as AWS from 'aws-sdk';
import { createReadStream } from 'fs';
import { Readable, Stream } from 'stream';
import * as lazystream from 'lazystream'; 
```

Enter fullscreen mode Exit fullscreen mode

首先配置 aws-sdk，使其在与 S3 通信时使用 keepalives，并限制最大连接数。这提高了效率，并有助于避免达到意外的连接限制。代替这个部分，你可以在你的 lambda 环境中设置`AWS_NODEJS_CONNECTION_REUSE_ENABLED`。

```
 // Set the S3 config to use keep-alives
    const agent = new https.Agent({ keepAlive: true, maxSockets: 16 });

    AWS.config.update({ httpOptions: { agent } }); 
```

Enter fullscreen mode Exit fullscreen mode

让我们从创建从 S3 获取数据的流开始。为了防止 S3 超时，流被“lazystream”包裹，这将延迟流的实际打开，直到归档程序准备好读取数据。

让我们假设您在`keys`中有一个键列表。对于每个键，我们需要创建一个读取流。为了跟踪键和流，让我们创建一个 S3DownloadStreamDetails 类型。“filename”最终将是 Zip 中的文件名，因此您可以在此阶段对其进行任何所需的转换。

```
 type S3DownloadStreamDetails = { stream: Readable; filename: string }; 
```

Enter fullscreen mode Exit fullscreen mode

现在对于我们的键数组，我们可以在它之后迭代来创建 S3StreamDetails 对象

```
 const s3DownloadStreams: S3DownloadStreamDetails[] = keys.map((key: string) => {
        return {
            stream: new lazystream.Readable(() => {
                console.log(`Creating read stream for ${fileToDownload.key}`);
                return s3.getObject({ Bucket: s3UGCBucket, Key: fileToDownload.key }).createReadStream();
            }),
            filename: key,
        };
    }); 
```

Enter fullscreen mode Exit fullscreen mode

现在，通过创建一个`Stream.PassThrough`对象并将其指定为`S3.PutObjectRequest`的参数体来准备上传端。

```
 const streamPassThrough = new Stream.PassThrough();
    const params: AWS.S3.PutObjectRequest = {
        ACL: 'private',
        Body: streamPassThrough
        Bucket: 'Bucket Name',
        ContentType: 'application/zip',
        Key: 'The Key on S3',
        StorageClass: 'STANDARD_IA', // Or as appropriate
    }; 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以开始上传过程了。

```
 const s3Upload = s3.upload(params, (error: Error): void => {
        if (error) {
            console.error(`Got error creating stream to s3 ${error.name}  ${error.message}  ${error.stack}`);
            throw error;
        }
    }); 
```

Enter fullscreen mode Exit fullscreen mode

如果你想监控上传过程，比如给用户反馈，那么你可以像这样给`httpUploadProgress`附加一个处理程序。

```
 s3Upload.on('httpUploadProgress', (progress: { loaded: number; total: number; part: number; key: string }): void => {
        console.log(progress); // { loaded: 4915, total: 192915, part: 1, key: 'foo.jpg' }
    }); 
```

Enter fullscreen mode Exit fullscreen mode

现在创建归档器

```
 const archive = Archiver('zip');
    archive.on('error', (error: Archiver.ArchiverError) => { throw new Error(`${error.name}  ${error.code}  ${error.message}  ${error.path}  ${error.stack}`); }); 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以连接归档程序，将数据传输到上传流，并将所有下载流附加到它上面

```
 await new Promise((resolve, reject) => {

        console.log('Starting upload');

        s3Upload.on('close', resolve);
        s3Upload.on('end', resolve);
        s3Upload.on('error', reject);

        archive.pipe(s3StreamUpload);
        s3DownloadStreams.forEach((streamDetails: S3DownloadStreamDetails) => archive.append(streamDetails.stream, { name: streamDetails.filename }));
        archive.finalize();
    }).catch((error: { code: string; message: string; data: string }) => { throw new Error(`${error.code}  ${error.message}  ${error.data}`); }); 
```

Enter fullscreen mode Exit fullscreen mode

最后等待上传者完成

```
 await s3Upload.promise(); 
```

Enter fullscreen mode Exit fullscreen mode

你就完了。

我已经用+10GB 的存档测试过了，效果非常好。我希望这对你有所帮助。