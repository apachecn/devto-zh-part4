# 已解决:“AnonymousUser”对象没有属性“profile”

> 原文：<https://dev.to/highcenburg/anonymoususer-object-has-no-attribute-profile-33ah>

<header>

# ![](img/540c2ba90e5a347bd57c676bb96dfee0.png) [ Django“用户”对象没有属性“配置文件”](https://stackoverflow.com/questions/57752859/django-user-object-has-no-attribute-profile)

Sep 2 '19 Comments: 5 Answers: 2[![](img/83f13d376e6608cc602ae93b1cdbae4e.png)1![](img/fd423aaf5fec73c645f97544689ea934.png)](https://stackoverflow.com/questions/57752859/django-user-object-has-no-attribute-profile) </header>

有人能帮我吗？我一直带着问题在这里转来转去，但得不到答案。

我得到了`'User' object has no attribute 'profile'`

这是我的用户模型

```
class Profile(models.Model):

    NARRATESTATUS = (
        ('PAS', 'Passed'),
        ('REV', 'For_Review'),
        ('ACC', 'Narration_Accepted'),
    )

    TRANSLATESTATUS = (
        ('PSD', 'Passed'),
        ('RVW',
```

…<button class="ltag__stackexchange--btn" type="button">[Open Full Question](https://stackoverflow.com/questions/57752859/django-user-object-has-no-attribute-profile)</button>