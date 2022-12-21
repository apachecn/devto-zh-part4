# 如何使用每个客户的路径划分 S3 存储区，并实现安全的文件访问

> 原文：<https://dev.to/bahadirbalban/how-to-divide-an-s3-bucket-with-per-customer-paths-and-enable-secure-file-access-5d8b>

[![One S3 box for all customers](img/465fd46fe1c748931aeee8f20522589a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ganE7LZi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://buzz-prod-photos.global.ssl.fastly.net/saasbox/img/47f1f1fb-d3da-47bc-8438-91beac767d44)

# 总结

您可以将单个 S3 存储桶划分为每个客户的路径，并允许这些客户只对他们自己的`/username`路径控制读或写访问。要做到这一点，可以给每个客户一个 AWS IAM 用户，并附加一个策略，只允许他们访问自己的`/username`路径。

客户可以使用签名的 S3 网址加速上传，并使他们的文件暂时安全地对公众开放(例如，在付费墙后面)。

使用案例:您为其他人托管 web 应用程序，这些人的客户注册了他们的服务，并从您客户的已签名 S3 url 下载他们购买的文件。该文件托管在您的 S3 存储桶上。

如果你想更进一步，让用户可以通过 CDN 下载他们的文件，Cloudfront 不会立即支持。这是因为每个用户都有自己的`/username`路径密钥，但是 Cloudfront 只有一个主密钥。您不能像生成 IAM 密钥那样为单个 S3 存储桶生成每个用户的 Cloudfront 密钥。下面分享了一个解决这个问题的方法，或者更简单:只需使用签名的 S3 网址进行下载。

# 详细信息

在构建 [SaaSBox](https://saasbox.net) 时，我需要创建一个存储托管解决方案，让每个客户都可以访问他们自己的文件，进行读写。我需要一个适用于许多用户的简单解决方案。我以一个 S3 桶结束，将它分成以`/username`开始的客户路径。

## 工作原理如下:

设置单个 s3 存储桶。每次新用户/客户注册时，您都要在 AWS 中创建一个新的 IAM 用户，并将以下策略附加到该用户:

```
{  "Version":  "2012-10-17",  "Statement":  [  {  "Sid":  "AllowGroupToSeeBucketListInTheConsole",  "Action":  [  "s3:ListAllMyBuckets",  "s3:GetBucketLocation"  ],  "Effect":  "Allow",  "Resource":  [  "arn:aws:s3:::*"  ]  },  {  "Sid":  "AllowRootAndHomeListingOfCompanyBucket",  "Action":  [  "s3:ListBucket"  ],  "Effect":  "Allow",  "Resource":  [  "arn:aws:s3:::my-s3-bucket-name"  ],  "Condition":  {  "StringEquals":  {  "s3:prefix":  [  "",  "/"  ],  "s3:delimiter":  [  "/"  ]  }  }  },  {  "Sid":  "AllowListingOfUserFolder",  "Action":  [  "s3:ListBucket"  ],  "Effect":  "Allow",  "Resource":  [  "arn:aws:s3:::my-s3-bucket-name"  ],  "Condition":  {  "StringLike":  {  "s3:prefix":  [  "${aws:username}/*",  "${aws:username}"  ]  }  }  },  {  "Sid":  "AllowAllS3ActionsInUserFolder",  "Action":  [  "s3:*"  ],  "Effect":  "Allow",  "Resource":  [  "arn:aws:s3:::my-s3-bucket-name/${aws:username}/*"  ]  }  ]  } 
```

该策略有一个`${aws:username}`占位符，这意味着它适用于附加了该策略的每个 IAM 用户。

**提示:**确保在创建 IAM 用户时也标记他们，以便您知道这些是您的服务的用户。

**注意:**您必须将策略附加到 IAM 用户，而不是 S3 时段。

### 将 S3 内容设为私有，仅通过签名的网址提供

您想要实现的是，您的 S3 存储桶内容始终是私有的，除了:

当你的用户想要的时候，他们应该能够写他们的目录。

他们应该能够在任何需要的时候公开他们的文件以供下载(就我而言，就在他们卖掉之后)。

您可以使用签名的 URL 来实现这一点。S3 桶支持上传和下载的签名网址。以下是生成签名 URL 所需的代码:

### S3 署名阅读网址:

```
/* S3 signed url for reading */
exports.get_file_read_presigned_url = function(fpath, ftype) {
    const url = s3.getSignedUrl('getObject', {
        Bucket: s3bucket.url,
        Key: fpath,
        ResponseContentType: ftype
    });
    return url;
} 
```

### S3 署名的网址为:

```
/* S3 signed url for uploading files */
exports.get_file_upload_presigned_url = function(fpath, ftype) {
    console.log("s3bucket.url:", s3bucket.url)
    const url = s3.getSignedUrl('putObject', {
        Bucket: s3bucket.url,
        Key: fpath,
        ACL: 'authenticated-read',
        ContentType: ftype
    });
    return url;
} 
```

### 使用 Cloudfront CDN 缓存文件

理想情况下，你应该在 S3 桶上设置 cloudfront，并使用 cloudfront 来签署 url，而不是使用 S3 签名的 URL 来阅读。下面是你通常如何为你自己的文件实现这个**。** 

```
const signer = new AWS.CloudFront.Signer(s3bucket.cf_accessKeyId, s3bucket.cf_privateKey);
const twoDays = 2*24*60*60*1000

/* Cloudfront signed url for reading */
exports.get_file_read_presigned_url = function(fpath, ftype ) {
    const signedUrl = signer.getSignedUrl({
        url: s3bucket.cdn_url + "/" + fpath,
        expires: Math.floor((Date.now() + twoDays)/1000), // Unix UTC timestamp for now + 2 days
    })
    return signedUrl;
} 
```

此时，您可以通过使用 S3 签名的 URL 进行上传，并使用 cloudfront 使它们可供读取，来加速向 S3 的上传。

然而，通过由客户密钥签名的 Cloudfront 提供文件并不是一个立即可行的方法。

### 在启用 Cloudfront 的情况下，您的用户使用基于用户的路径对 S3 文件进行细粒度访问

这是我想要实现的东西，例如，如果我可以创建 IAM 用户，每个用户使用自己的密钥在 S3 桶上访问目录，我也希望使用 CDN 如 cloudfront 提供他们的文件，让*他们*使用他们的密钥签署 URL。

不幸的是，Cloudfront 并没有立即支持这一点。例如，用例是您使用 AWS root 帐户为 Cloudfront 创建一些主密钥，并使用您的密钥签名所有文件。

如果您希望您的用户使用他们自己的密钥在 S3 bucket 上提供他们自己的目录路径，这在 Cloudfront 上是不可能的，因为您只有一个主密钥。

简单的解决方案是只使用 S3 签名的网址，而不使用 cloudfront。您可以在一个 s3 存储桶上服务数千个用户！

使用 Cloudfront 有一个解决方法，在这个链接中有描述:[如何在 Cloudfront 中使用 S3 签名的 URL。](https://advancedweb.hu/2018/11/15/s3_signed_urls_cloudfront/)

### CloudFront 工作区

本质上，S3 签名的 URL 每次生成时都会改变。因此，每个新的 url 都意味着由 Cloudfront 重新缓存，违背了拥有缓存的目的。因此，你要做的是，强制/破解 S3 签名的 url 生成功能，在一段时间内生成相同的 url，人为地将时间元素固定在一个窗口上。例如，对于当前小时+ 1 小时，告诉它只生成这个特定的 url。这样 CF 就可以缓存那个时期的 url。

如果通过 Cloudfront 直接生成 URL，就不会有这个问题，因为 CloudFront 可以直接访问文件。

但是，你可以做到这一点，通过固定 S3 生成的网址，并重新缓存文件每隔几个小时。