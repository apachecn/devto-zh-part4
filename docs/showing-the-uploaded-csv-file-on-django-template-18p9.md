# 已解决:在 Django 模板上显示上传的 csv 文件

> 原文：<https://dev.to/highcenburg/showing-the-uploaded-csv-file-on-django-template-18p9>

<header>

# ![](img/540c2ba90e5a347bd57c676bb96dfee0.png) [显示 Django 模板上上传的 csv 文件](https://stackoverflow.com/questions/57920042/showing-the-uploaded-csv-file-on-django-template)

Sep 13 '19 Comments: 3 Answers: 1[![](img/83f13d376e6608cc602ae93b1cdbae4e.png)0![](img/fd423aaf5fec73c645f97544689ea934.png)](https://stackoverflow.com/questions/57920042/showing-the-uploaded-csv-file-on-django-template) </header>

我有一个项目，接受上传。csv 文件，并通过 models.py 在管理上显示它。我想发生的是显示这些上传。模板上的 csv 文件。

我的观点是:

```
def data_upload(request):
    template = "home.html"

    if request.method == 'GET':
        return render(request, template)

    csv_file
```

…<button class="ltag__stackexchange--btn" type="button">[Open Full Question](https://stackoverflow.com/questions/57920042/showing-the-uploaded-csv-file-on-django-template)</button>