# 已解决:django 在/ 'file '处引发了 MultiValueDictKeyError

> 原文：<https://dev.to/highcenburg/django-raises-multivaluedictkeyerror-at-file-e9j>

<header>

# ![](img/540c2ba90e5a347bd57c676bb96dfee0.png) [ django 在/‘文件’处引发 MultiValueDictKeyError](https://stackoverflow.com/questions/57899513/django-raises-multivaluedictkeyerror-at-file)

Sep 12 '19 Comments: 4 Answers: 3[![](img/83f13d376e6608cc602ae93b1cdbae4e.png)3![](img/fd423aaf5fec73c645f97544689ea934.png)](https://stackoverflow.com/questions/57899513/django-raises-multivaluedictkeyerror-at-file) </header>

我试图创建一个视图来上传一个. csv 文件并在模板上解析它，但是它返回一个

```
MultiValueDictKeyError at /
'文件'
```

该模板具有

```

```
<form method="POST" enctype="multipart/form-data">{% csrf_token %}
<div class="file-field input-field">
<div class="btn">
<span>Upload a CSV FILE</span>
<input type="file" name="file">
</div>
<div class="file-path-wrapper">
 <input class="file-path
```

```

…<button class="ltag__stackexchange--btn" type="button">[Open Full Question](https://stackoverflow.com/questions/57899513/django-raises-multivaluedictkeyerror-at-file)</button>