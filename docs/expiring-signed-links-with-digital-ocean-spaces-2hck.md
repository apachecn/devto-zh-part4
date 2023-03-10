# 与数字海洋空间的过期(签名)链接

> 原文：<https://dev.to/monty5811/expiring-signed-links-with-digital-ocean-spaces-2hck>

关于如何使用 Django 在数字海洋空间上创建一个对象的过期链接的快速指南。(< -这是一个附属链接，顺便说一句，如果你注册，你将在 30 天内获得 50 美元)

TL；博士:

```
import boto3
from botocore.client import Config

session = boto3.session.Session()
client = session.client(
    "s3",
    region_name=AWS_S3_REGION_NAME,
    endpoint_url=AWS_S3_ENDPOINT_URL,
    aws_access_key_id=AWS_ACCESS_KEY_ID,
    aws_secret_access_key=AWS_SECRET_ACCESS_KEY,
    config=Config(signature_version="s3"),
)

url = client.generate_presigned_url(
    ClientMethod="get_object",
    Params={
        "Bucket": "bucket-name",
        "Key": "object-key",
    },
    ExpiresIn=60 * 60,
) 
```

Enter fullscreen mode Exit fullscreen mode

## 为什么

如果您将您的资源(图像、pdf、音频文件等)存储在像 Spaces 这样的对象存储中，但是出于某种原因您想要限制对这些对象的访问，您可以创建一个签名链接，在某个定义的时间后过期。

例如，我托管来自另一个空间布道主持人的所有音频文件，但是我需要跟踪下载次数，以便为我的客户提供分析。我可以在 Django 视图中注册一个下载，然后重定向到过期的空间 URL。
这样，所有的下载都必须通过我的服务器，生成过期的网址，我可以记录每一次下载。

## 如何

数字海洋空间与亚马逊 S3 的 API 兼容，所以我们可以使用[boto 3](https://github.com/boto/boto3)&[django-storages](https://github.com/jschneier/django-storages)——我假设你已经有了`django-storages`设置([DO 空间的指令](https://django-storages.readthedocs.io/en/latest/backends/digital-ocean-spaces.html))。

安装`django-storages`后，你的`settings.py`应该包含类似这样的内容；

```
# settings.py 
AWS_QUERYSTRING_AUTH = False
AWS_DEFAULT_ACL = "public-read"
AWS_ACCESS_KEY_ID = os.environ["DO_SPACES_ACCESS_KEY"]
AWS_SECRET_ACCESS_KEY = os.environ["DO_SPACES_SECRET"]
AWS_STORAGE_BUCKET_NAME = "bucket-name"
AWS_S3_REGION_NAME = "do-spaces-region"
AWS_S3_ENDPOINT_URL = f"https://{AWS_S3_REGION_NAME}.digitaloceanspaces.com"
AWS_S3_CUSTOM_DOMAIN = (
    f"{AWS_STORAGE_BUCKET_NAME}.{AWS_S3_REGION_NAME}.cdn.digitaloceanspaces.com"
)
AWS_S3_OBJECT_PARAMETERS = {"CacheControl": "max-age=86400"}AWS_MEDIA_LOCATION = "yash-media"
AWS_S3_FILE_OVERWRITE = False # safer behaviour than the default AWS_S3_OBJECT_PARAMETERS = {"CacheControl": "max-age=86400"} # do some caching, why not 
```

Enter fullscreen mode Exit fullscreen mode

我们将添加一个 boto3 客户端:

```
# settings.py 
# client for expiring urls: aws_session = boto3.session.Session()
AWS_CLIENT = aws_session.client(
    "s3",
    region_name=AWS_S3_REGION_NAME,
    endpoint_url=AWS_S3_ENDPOINT_URL,
    aws_access_key_id=AWS_ACCESS_KEY_ID,
    aws_secret_access_key=AWS_SECRET_ACCESS_KEY,
    config=Config(signature_version="s3"),
) 
```

Enter fullscreen mode Exit fullscreen mode

而且我们应该有一个带`FileField`
的模型

```
# models.py 
from django.db import models

class Asset(models.Model):
    name = models.CharField(max_length=1000)
    private_file = models.FileField()
    # other fields here 
```

Enter fullscreen mode Exit fullscreen mode

生成过期链接就是使用 boto3 库的问题，所以
让我们把它作为一个方法添加到模型中:

```
# models.py 
from django.conf import settings
from django.db import models

class Asset(models.Model):
    name = models.CharField(max_length=1000)
    private_file = models.FileField()
    # other fields here 
    @property
    def storage_key(self):
        return f"{self.private_file.storage.location}/{self.private_file.name}"

    def get_expiring_url(self, expires_in=1800):
        """
        Generates a permissioned url that will expire at `expires_in` seconds.
        Defaults to 30 minutes.
        """
        if self.private_file is not None:
            url = settings.AWS_CLIENT.generate_presigned_url(
                ClientMethod="get_object",
                Params={
                    "Bucket": settings.AWS_STORAGE_BUCKET_NAME,
                    "Key": self.storage_key,
                },
                ExpiresIn=expires_in,
            )
            return url

        raise Http404 
```

Enter fullscreen mode Exit fullscreen mode

我们可以这样重定向用户:

```
# views.py 
from django.shortcuts import get_object_or_404
from django.views.generic import RedirectView

class Download(RedirectView):
    def get_redirect_url(self, *args, **kwargs):
        """
        Override this method so the view doesn't try to do any 
        string interpolation.
        """
        return self.url

    def get(self, request, *args, **kwargs):
        asset = get_object_or_404(models.Asset, pk=kwargs["pk"])
        # TODO: register a download or check user has permission here
        self.url = asset.get_expiring_url()
        return super().get(request, *args, **kwargs) 
```

Enter fullscreen mode Exit fullscreen mode

注意:您必须将对象上的 ACL 设置为`private`，否则任何人都可以在没有签名的 URL 的情况下访问文件。

我们可以通过将整个桶私有来做到这一点:

```
# settings.py 
AWS_DEFAULT_ACL = "private" 
```

Enter fullscreen mode Exit fullscreen mode

或者我们可以用几个助手方法切换单个对象:

```
# models.py 
class Asset(models.Model):
    # other methods from above 
    def set_private(self):
        self._set_acl("private")

    def set_public_read(self):
        self._set_acl("public-read")

    def _set_acl(self, acl):
        settings.AWS_CLIENT.put_object_acl(
            Bucket=settings.AWS_STORAGE_BUCKET_NAME,
            Key=self.storage_key, ACL=acl
        ) 
```

Enter fullscreen mode Exit fullscreen mode