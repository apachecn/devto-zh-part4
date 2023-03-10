# AWS: S3 跨区域复制配置和对象删除标记复制

> 原文：<https://dev.to/setevoy/aws-s3-cross-region-replication-configuration-and-objects-deletemarkers-replication-234m>

[![](img/561b8490de4ebd015401f8bc5a20bd1a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eCoKOfdn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rtfm.co.ua/wp-content/uploads/2014/11/aws-logo-square-02-e1417012834176.png) 此刻我正在为我们的项目配置一个新的 CDN。

我将在这里使用 CloudFront 和 Cloudflare，因此需要创建两个不同名称的专用桶—*cdn.cfr.example.com*=>CloudFront 和*cdn.cfl.example.com*=>Cloudflare。

为了避免每次都将数据复制到两个桶中，可以使用 AWS S3 跨区域复制，因此来自*桶-1* 的数据将被复制到*桶-2* 。

文档—[跨区域复制](https://docs.aws.amazon.com/en_us/AmazonS3/latest/dev/crr.html)。

CRR 先决条件:

1.  两个存储桶都必须启用 S3 版本控制(请参见)。[使用版本控制](https://docs.aws.amazon.com/AmazonS3/latest/dev/Versioning.html)
2.  两个存储桶必须在不同的区域创建
3.  两个存储桶都必须拥有在它们之间进行复制的权限

CRR 限制:

1.  配置 CRR 后，不会复制源存储桶中已有的文件(即，只会复制新文件)
2.  无法创建复制链，即*Bucket-A =>Bucket-B =>Bucket-C*

### AWS S3 跨区域复制设置

创建两个存储桶:

[![](img/772df5a13db4cf8a9b5a4824e34bd9a0.png "AWS: настройка S3 Cross-Region Replication и удаление файлов при репликации")](https://rtfm.co.ua/wp-content/uploads/2019/07/Screenshot_20190715_152839.png)

对于两个启用*版本控制*:

[![](img/4642e3af96e151589da5009e277e110c.png "AWS: настройка S3 Cross-Region Replication и удаление файлов при репликации")](https://rtfm.co.ua/wp-content/uploads/2019/07/Screenshot_20190715_152929.png)

在 source-bucket*Bt trm-crr-source*转到*管理>复制*，点击*添加规则*:

[![](img/6925e4cde899efd4c52c74a19eed2fd3.png "AWS: настройка S3 Cross-Region Replication и удаление файлов при репликации")](https://rtfm.co.ua/wp-content/uploads/2019/07/Screenshot_20190715_153125.png)

设置从此存储桶复制所有内容:

[![](img/39f35b4a00e7d8845641d1f76e0ec777.png "AWS: настройка S3 Cross-Region Replication и удаление файлов при репликации")](https://rtfm.co.ua/wp-content/uploads/2019/07/Screenshot_20190715_153214.png)

点击*下一个*，设置接收桶名称:

[![](img/17b038acfcc523ef0d55bc7609f01f76.png "AWS: настройка S3 Cross-Region Replication и удаление файлов при репликации")](https://rtfm.co.ua/wp-content/uploads/2019/07/Screenshot_20190715_153256.png)

下一步–权限和 IAM 角色。

选择了*创建新的 IAM 角色*，设置其名称:

[![](img/72afde5f5b745a6c7ae4e521c71b1f57.png "AWS: настройка S3 Cross-Region Replication и удаление файлов при репликации")](https://rtfm.co.ua/wp-content/uploads/2019/07/Screenshot_20190715_153352.png)

保存:

[![](img/09a1e1853c1b4a8a0d89b1fff61e2dc7.png "AWS: настройка S3 Cross-Region Replication и удаление файлов при репликации")](https://rtfm.co.ua/wp-content/uploads/2019/07/Screenshot_20190715_153424.png)

[![](img/612ae51e149c136deff129aa67b4d008.png "AWS: настройка S3 Cross-Region Replication и удаление файлов при репликации")](https://rtfm.co.ua/wp-content/uploads/2019/07/Screenshot_20190715_153448.png)

#### 复制测试

上传一个测试文件到源 *bttrm-crr-source* 桶:

```
$ touch testfile.txt
$ aws --profile bm-backend --region us-east-2 s3 cp testfile.txt s3://bttrm-crr-source
$ upload: ./testfile.txt to s3://bttrm-crr-source/testfile.txt 
```

检查是否存在:

```
$ aws --profile bm-backend --region us-east-2 s3 ls s3://bttrm-crr-source
2019-07-15 15:36:38          0 testfile.txt 
```

并检查目的桶:

```
$ aws --profile bm-backend --region us-west-1 s3 ls s3://bttrm-crr-dest
2019-07-15 15:36:38          0 testfile.txt 
```

文件在，一切正常。

### 从 S3 跨区域复制中删除文件并删除标记

如果您只是从当前源存储桶中删除一个文件，它将在那里被删除，但不会在目标存储桶中被删除。

在源文件中删除:

```
$ aws --profile bm-backend --region us-east-2 s3 rm s3://bttrm-crr-source/testfile.txt
delete: s3://bttrm-crr-source/testfile.txt 
```

在目的地检查:

```
$ aws --profile bm-backend --region us-west-1 s3 ls s3://bttrm-crr-dest
2019-07-15 15:36:38          0 testfile.txt 
```

文件还在这里。

要从 S3 版本控制下删除对象，AWS 会添加一个名为`DeleteMarker`的特殊标记(参见。[使用删除标记](https://docs.aws.amazon.com/en_us/AmazonS3/latest/dev/DeleteMarker.html):

```
$ aws --profile bm-backend --region us-west-1 s3api list-object-versions --bucket bttrm-crr-source
{
  "Versions": [
    {
      "ETag": "\"d41d8cd98f00b204e9800998ecf8427e\"",
      "Size": 0,
      "StorageClass": "STANDARD",
      "Key": "testfile.txt",
      "VersionId": "Qc5.Z2XumG6uilJ99XcHJ1vIfzMFcx6C",
      "IsLatest": false,
      "LastModified": "2019-07-15T12:36:38.000Z",
      "Owner": {
      "DisplayName": "example.aws",
      "ID": "9365528c1a92cd01abba171eb7b95c352a330a40ceb28b420c9462fac5891bd2"
      }
    }
  ],
  "DeleteMarkers": [
    {
      "Owner": {
      "DisplayName": "example.aws",
      "ID": "9365528c1a92cd01abba171eb7b95c352a330a40ceb28b420c9462fac5891bd2"
},
      "Key": "testfile.txt",
      "VersionId": "PIr6ZOPcdi9oFP8y0rMXkRhrFuKpDQ4P",
     "IsLatest": true,
     "LastModified": "2019-07-15T12:39:24.000Z"
    }
  ]
} 
```

但是这个标记不会被复制到目标桶，参见[亚马逊 S3 复制什么](https://docs.aws.amazon.com/en_us/AmazonS3/latest/dev/crr-what-is-isnot-replicated.html):

*   如果您在没有指定对象版本 ID 的情况下发出删除请求，亚马逊 S3 会添加一个删除标记。亚马逊 S3 处理删除标记如下:

    *   如果您使用的是最新版本的复制配置，也就是说，您在复制配置规则中指定了`Filter`元素，亚马逊 S3 不会复制删除标记。
    *   如果不指定`Filter`元素，亚马逊 S3 会假设复制配置是以前版本的 V1。在早期版本中，亚马逊 S3 以不同的方式处理删除标记的复制。更多信息，请参见[向后兼容性](https://docs.aws.amazon.com/en_us/AmazonS3/latest/dev/crr-add-config.html#crr-backward-compat-considerations)。
*   如果您在删除请求中指定了要删除的对象版本 ID，Amazon S3 会删除源存储桶中的对象版本，但不会复制目标存储桶中的删除。换句话说，它不会从目标桶中删除相同的对象版本。这可以保护数据免受恶意删除。

检查目的桶中的文件:

```
$ aws --profile bm-backend --region us-west-1 s3api list-object-versions --bucket bttrm-crr-dest
{
  "Versions": [
    {
      "ETag": "\"d41d8cd98f00b204e9800998ecf8427e\"",
      ...
      "Key": "testfile.txt",
      "VersionId": "Qc5.Z2XumG6uilJ99XcHJ1vIfzMFcx6C",
      ...
    }
  ]
} 
```

没有人在场。

检查复制设置:

```
$ aws --profile bm-backend --region us-west-1 s3api get-bucket-replication --bucket bttrm-crr-source  --query 'ReplicationConfiguration.Rules[*].DeleteMarkerReplication' --output text

Disabled 
```

`DeleteMarkerReplication`–`"Status": "Disabled"`，好的。

#### `IAM s3:ReplicateDelete`

首先，检查所使用的 IAM 角色(参见[设置跨区域复制的权限](https://docs.aws.amazon.com/en_us/AmazonS3/latest/dev/setting-repl-config-perm-overview.html))。

找到它的名字:

[![](img/4dd018996d614b11c710f9c7a93c87f4.png "AWS: настройка S3 Cross-Region Replication и удаление файлов при репликации")](https://rtfm.co.ua/wp-content/uploads/2019/07/Screenshot_20190717_110828.png)

并在 IAM 中找到这个角色:

[![](img/3589b786d181acac7f0512489248061b.png "AWS: настройка S3 Cross-Region Replication и удаление файлов при репликации")](https://rtfm.co.ua/wp-content/uploads/2019/07/Screenshot_20190717_110922.png)

检查一下——*动作*中的`"s3:ReplicateDelete"`必须出现:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "s3:Get*",
                "s3:ListBucket"
            ],
            "Effect": "Allow",
            "Resource": [
                "arn:aws:s3:::bttrm-crr-source",
                "arn:aws:s3:::bttrm-crr-source/*"
            ]
        },
        {
            "Action": [
                "s3:ReplicateObject",
                "s3:ReplicateDelete",
                "s3:ReplicateTags",
                "s3:GetObjectVersionTagging"
            ],
            "Effect": "Allow",
            "Resource": "arn:aws:s3:::bttrm-crr-dest/*"
        }
    ]
} 
```

#### S3 的斗策

现在–导出现有的 S3 策略:

```
$ aws --profile bm-backend --region us-west-1 s3api get-bucket-replication --bucket bttrm-crr-source > bttrm-crr-source-origin.json 
```

检查其内容:

```
{
    "ReplicationConfiguration": {
        "Role": "arn:aws:iam::534***385:role/service-role/s3crr_role_for_bttrm-crr-source_to_bttrm-crr-dest",
        "Rules": [
            {
                "ID": "bttrm-s3-crr-replica",
                "Priority": 1,
                "Filter": {},
                "Status": "Enabled",
                "Destination": {
                    "Bucket": "arn:aws:s3:::bttrm-crr-dest"
                },
                "DeleteMarkerReplication": {
                    "Status": "Disabled"
                }
            }
        ]
    }
} 
```

现在去掉`ReplicationConfiguration`、`"Filter": {}`、`"Priority": 1`、`DeleteMarkerReplication`参数，加上`"Prefix": ""`，这样这个策略就会像版本 1:

```
{
        "Role": "arn:aws:iam::534***385:role/service-role/s3crr_role_for_bttrm-crr-source_to_bttrm-crr-dest",
        "Rules": [
            {
                "ID": "bttrm-s3-crr-replica",
                "Prefix": "",
                "Status": "Enabled",
                "Destination": {
                    "Bucket": "arn:aws:s3:::bttrm-crr-dest"
                }
            }
        ]
} 
```

问题是 CRR 配置版本 2 这时仍然不能做`DeleteMarkers`复制，AWS 支持回复是:

> 我确实看了你的博客，你提供的建议似乎是正确的。事实上，这也是我的建议。内部团队也在努力将删除复制功能引入 V2，但这可能需要一些时间。V2 在后端实现的方式可能与 V1 没有什么不同，直接使用这个特性可能会导致一些问题。不幸的是，我不知道什么时候会结束。在那之前，我相信你的博客能够帮助 AWS 用户:)

应用此配置:

```
$ aws --profile bm-backend --region us-west-1 s3api put-bucket-replication --bucket bttrm-crr-source --replication-configuration file://bttrm-crr-source.json 
```

上传一些新文件到源桶:

```
$ aws --profile bm-backend --region us-east-2 s3 cp test.file s3://bttrm-crr-source 
```

删除:

```
$ aws --profile bm-backend --region us-east-2 s3 rm s3://bttrm-crr-source/test.file 
```

现在检查源桶:

```
$ aws --profile bm-backend --region us-west-1 s3api list-object-versions --bucket bttrm-crr-source
{
  "Versions": [
    {
      "ETag": "\"d41d8cd98f00b204e9800998ecf8427e\"",
      ...
      "Key": "test.file",
      "VersionId": "0mkfsrIRdkGCgL.zKgMyMXjo\_UydigIt",
      ...
    },
    {
      "ETag": "\"d41d8cd98f00b204e9800998ecf8427e\"",
      ...
      "Key": "testfile.txt",
      "VersionId": "Qc5.Z2XumG6uilJ99XcHJ1vIfzMFcx6C",
      ...
    }
  ],
  "DeleteMarkers": [
    {
      ...
      "Key": "test.file",
      "VersionId": "1kpKJlgOM7xpFnH4qsEp0EzqhCE1HFMX",
      ...
    },
    {
      ...
      "Key": "testfile.txt",
      "VersionId": "PIr6ZOPcdi9oFP8y0rMXkRhrFuKpDQ4P",
      ...
    }
  ]
} 
```

太好了——两个文件都找到了，都有`DeleteMarkers`。

并检查目的桶:

```
$ aws --profile bm-backend --region us-west-1 s3api list-object-versions --bucket bttrm-crr-dest
{
  "Versions": [
    {
      "ETag": "\"d41d8cd98f00b204e9800998ecf8427e\"",
      ...
      "Key": "test.file",
      "VersionId": "0mkfsrIRdkGCgL.zKgMyMXjo\_UydigIt",
      ...
    },
    {
      "ETag": "\"d41d8cd98f00b204e9800998ecf8427e\"",
      ...
      "Key": "testfile.txt",
      "VersionId": "Qc5.Z2XumG6uilJ99XcHJ1vIfzMFcx6C",
     ...
    }
    ],
    "DeleteMarkers": [
      {
        ...
        "Key": "test.file",
        "VersionId": "1kpKJlgOM7xpFnH4qsEp0EzqhCE1HFMX",
        ...
      }
   ]
} 
```

*testfile.txt* 仍然没有`DeleteMarker`——没关系，因为在我们对`DeleteMarkers`行为进行更改之前，它已经被删除了。

但是对于已经存在的*test . file*–`DeleteMarker`，它是从源中复制的，所以这个文件现在也被“删除”:

```
$ aws --paws --profile bm-backend --region us-west-1 s3 ls s3://bttrm-crr-dest
2019-07-15 15:36:38          0 testfile.txt 
```

没有找到*测试文件*。

完成了。

### 类似的帖子

*   <small>2018 年 7 月 3 日</small>[【AWS:云组】【б，IAM】，冰生命周期通知](https://rtfm.co.ua/aws-cloudformation-primer-sozdaniya-s3-korziny-iam-polzovatelya-glacier-lifecycle-i-sns-notification/)
*   <small>2018 年 11 月 27 日</small>[【AWS:云形成–S3′aks′aks′应用负载平衡器](https://rtfm.co.ua/aws-cloudformation-s3-korzina-dlya-logov-application-load-balancer/)
*   <small>10/13/2017</small>[【AWS:iam bash】近东救济工程处(近东救济工程处)MySQL/MariaDB 近东救济工程处(近东救济工程处)](https://rtfm.co.ua/aws-iam-i-bash-skript-bekapa-mysqlmariadb-baz-v-aws-s3/)