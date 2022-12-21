# AWS 媒体服务

> 原文：<https://dev.to/vikashagrawal/aws-media-services-4pi5>

# 弹性转码器

它转换并存储多种媒体，并将其转换为系统可适应的格式(android、ios)
工作流程

```
o   The audio files get stored in S3, which invokes lambda function.
o   This lambda function would invoke Elastic Transcoder, which would convert to different format.
o   And then it would be stored in S3. 
```

根据您转码的分钟数和转码的分辨率付费。

# 媒体转换

它将媒体转换为存储

# 媒体直播

它有助于直播

# 媒体包

它提供完整的包装。

# 媒体商店

它存储媒体

# 媒体裁缝

它有助于将广告等内容投放到媒体中。

从媒体的生命周期来看，上述服务可以通过以下方式联系起来:

```
Media ==> Media Live (Live)
Media ==> Media Convert (conversion) ==> Media Store (Storage) ==> Media Tailor (Putting advertisement) ==> Media Package (complete package) 
```