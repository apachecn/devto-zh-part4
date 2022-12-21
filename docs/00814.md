# 已解决:连接两个模型以创建下拉列表

> 原文：<https://dev.to/highcenburg/connecting-two-models-to-create-a-dropdown-4f1h>

<header>

# ![](img/540c2ba90e5a347bd57c676bb96dfee0.png) [连接两个模型创建下拉菜单](https://stackoverflow.com/questions/58056961/connecting-two-models-to-create-a-dropdown)

Sep 23 '19 Comments: 4 Answers: 1[![](img/83f13d376e6608cc602ae93b1cdbae4e.png)0![](img/fd423aaf5fec73c645f97544689ea934.png)](https://stackoverflow.com/questions/58056961/connecting-two-models-to-create-a-dropdown) </header>

我想知道我如何连接两个模型来创建一个相互依赖的下拉列表。

截至发稿时，这些是我的视频模型:

```
class AMLVideo(models.Model):

    LANGUAGE = (
        ('LAN', 'Language'),
        ('FR', 'French'),
        ('EN', 'English'),
        ('HIN', 'Hindi'),
        ('SPA', 'Spanish'),
        ('GER', 'German'),
    )

    LEVEL =
```

…<button class="ltag__stackexchange--btn" type="button">[Open Full Question](https://stackoverflow.com/questions/58056961/connecting-two-models-to-create-a-dropdown)</button>