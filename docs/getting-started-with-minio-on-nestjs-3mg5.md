# nestjs 上的 minio 入门

> 原文：<https://dev.to/rubiin/getting-started-with-minio-on-nestjs-3mg5>

## 描述

MinIO 是在 Apache 许可证 v2.0 下发布的对象存储服务器。它与亚马逊 S3 云存储服务兼容。它最适合存储照片、视频、日志文件、备份和容器/虚拟机映像等非结构化数据。对象的大小可以从几千字节到最大 5TB 不等。

MinIO server 足够轻，可以和应用栈捆绑，类似 NodeJS，Redis，MySQL。

这是 [Nest](https://github.com/nestjs/nest) 的 [nest-minio](https://github.com/rubiin/nest-minio) 模块。该快速入门指南将向您展示如何安装客户端 SDK 并执行一个示例 JavaScript 程序。要获得 API 和示例的完整列表，请查看 [JavaScript 客户端 API 参考](https://docs.min.io/docs/javascript-client-api-reference)文档。

本文档假设您已经有了一个有效的 [nodejs](http://nodejs.org/) 设置。

## 安装

```
$ npm i --save nest-minio 
```

Enter fullscreen mode Exit fullscreen mode

## 从 NPM 下载

```
npm install --save nest-minio 
```

Enter fullscreen mode Exit fullscreen mode

## 初始化 MinIO 客户端

您需要五个项目才能连接到 MinIO 对象存储服务器。

| 参数 | 描述 |
| --- | --- |
| 端点 | 对象存储服务的 URL。 |
| 港口 | TCP/IP 端口号。该输入是可选的。对于 HTTP，默认值设置为`80`，对于 HTTPs，默认值设置为`443`。 |
| accessKey | 访问密钥类似于唯一标识您帐户的用户 ID。 |
| 秘密钥匙 | 密钥是您帐户的密码。 |
| 使用 SSL | 将此值设置为“真”以启用安全(HTTPS)访问 |

通过将 minio 模块导入为:
来提供其凭证

```
import { Module } from '@nestjs/common';
import { NestMinioClientController } from './nest-minio-client.controller';
import { NestMinioModule } from '../nest-minio.module';

@Module({
  controllers: [NestMinioClientController],
  imports: [
    NestMinioModule.register({
      endPoint: 'play.min.io',
      port: 9000,
      useSSL: true,
      accessKey: 'Q3AM3UQ867SPQQA43P2F',
      secretKey: 'zuf+tfteSlswRu7BJ86wekitnifILbZam1KYY3TG',
    }),
  ],
})
export class NestMinioClientModule {}

}); 
```

Enter fullscreen mode Exit fullscreen mode

然后，您可以在控制器或服务中使用它，方法是将它注入到控制器中:

```
 constructor(@Inject(MINIO_CONNECTION) private readonly minioClient) {} 
```

Enter fullscreen mode Exit fullscreen mode

## 快速入门示例-文件上传器

这个示例程序连接到一个对象存储服务器，在服务器上创建一个 bucket，然后将一个文件上传到这个 bucket。

在这个例子中，我们将使用运行在 [https://play.min.io](https://play.min.io) 的 MinIO 服务器。请随意使用这项服务进行测试和开发。此示例中显示的访问凭证对公众开放。

```
 import { Controller, Get, Inject } from '@nestjs/common';
import { MINIO_CONNECTION } from '../constants';

@Controller()
export class NestMinioClientController {
  constructor(@Inject(MINIO_CONNECTION) private readonly minioClient) {}

  @Get()
  index() {
    const file = '/tmp/app.zip';

    const metaData = {
      'Content-Type': 'application/octet-stream',
      'X-Amz-Meta-Testing': 1234,
      example: 5678,
    };
    // Using fPutObject API upload your file to the bucket europetrip.
    this.minioClient.fPutObject(
      'europetripxxx3',
      'app.zip',
      file,
      metaData,
      function(err, etag) {
        if (err) {
          return console.log(err);
        }
        console.log('File uploaded successfully.');
      },
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

[![Buy Me A Coffee](img/2f56ffef578f338c375b41d10c3684b7.png)](https://www.buymeacoffee.com/XbgWxt567)