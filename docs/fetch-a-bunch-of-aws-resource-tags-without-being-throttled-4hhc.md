# 获取一堆 AWS 资源标签(没有节流！)

> 原文：<https://dev.to/ajkerrigan/fetch-a-bunch-of-aws-resource-tags-without-being-throttled-4hhc>

TL；DR-[资源~~组~~标签 API](https://docs.aws.amazon.com/resourcegroupstagging/latest/APIReference/Welcome.html) 可以帮助你批量获取资源标签，即使你不使用[资源组](https://docs.aws.amazon.com/ARG/latest/userguide/welcome.html)！

## 问题

您希望以编程方式构建一个活动 AWS 资源列表。对于像 EC2 这样的服务，您调用 [DescribeInstances](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeInstances.html) 并在响应中包含标签。耶！

对于其他服务(这里我将使用 RDS)，您需要分两步完成:

1.  扫描活动资源( [DescribeDBInstances](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBInstances.html)
2.  获取这些资源的标签

有几种不同的方法来处理第二步，最简单的一种方法会随着资源数量的增加而受到影响。

## 抓取标签-最简单的方法

RDS API 有一个 [ListTagsForResource](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ListTagsForResource.html) 动作。如果你有资源并需要它们的标签，这是最明显的获取方式。问题是你一次只能为*的一个*资源获取标签。

这有点令人沮丧，但是通过一点循环，这看起来是可行的

```
import boto3

rds = boto3.client('rds')

# Build a mapping of instance ARNs to details instances = {
    instance['DBInstanceArn']: instance
    for instance in rds.describe_db_instances()['DBInstances']
}

# Add tag detail to each instance for arn, instance in instances.items():
    instance['Tags'] = rds.list_tags_for_resource(ResourceName=arn).get('TagList') 
```

Enter fullscreen mode Exit fullscreen mode

这对于少数资源来说很好，但最终会因为几个原因而受阻:

1.  `DescribeDBInstances`在一次调用中最多只能返回 100 条记录。你们中的一些人可能注意到了这个疏忽。幸运的是，我们可以通过使用 [boto3](http://boto3.readthedocs.io/) 出色的[分页器](https://boto3.amazonaws.com/v1/documentation/api/latest/guide/paginators.html)支持来解决这个问题。

2.  如果您解决了分页问题，这意味着您拥有超过 100 个资源。这也意味着您在短时间内用超过 100 个`ListTagsForResource`调用轰炸 RDS API。虽然 AWS 目前没有公布 RDS API 的速率限制，但它们仍然会咬你一口。

## 避免 API 速率限制/节流

如果您在尝试获取资源标签时遇到 API 速率限制，您可能会有两种想法:

1.  我负责[使用回退/重试逻辑](https://docs.aws.amazon.com/general/latest/gr/api-retries.html)就好了。
2.  尽管如此，我真的希望我能一次为不止*一个*资源拉标签！

## 资源~~组~~标记 API

我必须在这里说实话，我完全忽略了[资源组标记 API](https://docs.aws.amazon.com/resourcegroupstagging/latest/APIReference/Welcome.html) 很长一段时间。我不怎么使用[资源组](https://docs.aws.amazon.com/ARG/latest/userguide/welcome.html)，我错误地认为所谓的资源组标记 API 旨在管理资源组的标记。

我仍然认为这是一个合理的假设...

但是对于我们这里的目的，让我们具体看一下 [GetResources](https://docs.aws.amazon.com/resourcegroupstagging/latest/APIReference/API_GetResources.html) 动作。正如医生指出的，它:

> 返回位于 AWS 帐户的指定区域中的所有已标记或以前标记的资源。

好吧，那怎么样！这意味着像这样的代码可以帮助找到给定帐户和地区中的所有 DB 实例标签:

```
instance_tags = rds.get_resources(ResourceTypeFilters=['rds:db']) 
```

Enter fullscreen mode Exit fullscreen mode

我们仍然需要处理分页响应来处理 100 多个资源，但这已经是一个巨大的胜利:

1.  在最极端的情况下，我们将 API 调用次数减少了 100 倍。
2.  这种减少的调用次数甚至不再以 RDS API 为目标。

这意味着我们可以更快地获得所需的数据，降低出错风险，并减少对同一客户和地区的其他人的影响。

## 把所有的东西放在一起

考虑到所有这些，下面是获取 RDS 实例和标记列表的一种方法。

```
from itertools import chain

import boto3

rds = boto3.client('rds')
tagging = boto3.client('resourcegroupstaggingapi') 
```

Enter fullscreen mode Exit fullscreen mode

我们一会儿将使用`chain`来使列表的工作变得更好。

```
# Build a mapping of instance ARNs to details paginator = rds.get_paginator('describe_db_instances')
instances = {
    instance['DBInstanceArn']: instance
    for page in paginator.paginate()
    for instance in page['DBInstances']
} 
```

Enter fullscreen mode Exit fullscreen mode

这是我们之前构建的映射的分页友好版本。

```
# Fetch tag data for all tagged (or previously tagged) RDS DB instances paginator = tagging.get_paginator('get_resources')
tag_mappings = chain.from_iterable(
    page['ResourceTagMappingList']
    for page in paginator.paginate(ResourceTypeFilters=['rds:db'])
) 
```

Enter fullscreen mode Exit fullscreen mode

分页器给了我们一个页面集合，每个页面都有一个结果集合。 [chain.from_iterable()](https://docs.python.org/3/library/itertools.html#itertools.chain.from_iterable) 帮助我们将这个“列表列表”视为一个单独的集合。

使用`rds:db`作为资源类型过滤器可以确保我们只获取 RDS DB 实例的标记，而不是带来其他 RDS 资源(比如快照)。

```
# Add tag detail to each instance for tag_mapping in tag_mappings:
    # Convert list of Key/Value pairs to dict for convenience
    tags = {tags['Key']: tags['Value'] for tags in tag_mapping['Tags']}

    instances[tag_mapping['ResourceARN']]['Tags'] = tags 
```

Enter fullscreen mode Exit fullscreen mode

多亏了`chain.from_iterable()`，我们可以循环遍历`tag_mappings`，就像它是一个平面列表一样。

由于拥有一个标记字典通常比一个键/值对列表更有用，我们也可以转换它。

## 鸣谢

大呼这里出了[云托管](https://cloudcustodian.io/docs/index.html)项目。它的[集中获取标记的方法](https://github.com/cloud-custodian/cloud-custodian/blob/94c60ff7d5067947026b5216c3d275518c0677ed/c7n/tags.py#L81-L115)帮助我认识到我长期忽视资源组标记 API 是多么的错误。

对于超越官方文档的 AWS 讨论，我发现 AWS 回购和 Slack channel 的[开放指南非常有用。](https://github.com/open-guides/og-aws/)

## 反馈

如果你已经读到这里，感谢你的阅读！喜欢聊 Python 和/或 AWS，请问好:)。

如果你有改进这篇文章的建议，或者更好的大规模获取标签的方法，请在评论中发表。