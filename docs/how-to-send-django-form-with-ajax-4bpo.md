# 如何用 AJAX 发送 Django 表单

> 原文：<https://dev.to/thepylot/how-to-send-django-form-with-ajax-4bpo>

怎么了 **DEV** Network？

在这个快速教程中，我将向你展示如何使用 AJAX 在不刷新页面的情况下发布 Django 表单。

我假设您已经创建了您的项目。让我们从在 **models.py** 中创建非常简单的 Post 模型开始

```
from django.db import models

class Post (models.Model):
    title = models.CharField(max_length=50)
    description = models.TextField()

    def __str__(self):
        return self.title 
```

Enter fullscreen mode Exit fullscreen mode

一旦创建了 open **views.py** 并插入以下代码:

```
from django.shortcuts import render
from django.http import JsonResponse
from .models import Post

def create_post(request):
    posts = Post.objects.all()
    response_data = {}

    if request.POST.get('action') == 'post':
        title = request.POST.get('title')
        description = request.POST.get('description')

        response_data['title'] = title
        response_data['description'] = description

        Post.objects.create(
            title = title,
            description = description,
            )
        return JsonResponse(response_data)

    return render(request, 'create_post.html', {'posts':posts}) 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，我们导入了 **JsonResponse** ，这是一个 HttpResponse 子类，有助于创建 JSON 编码的响应。默认的内容类型头设置为 application/json。第一个参数 data 应该是一个 dict 实例。我们将使用 JSON 数据立即显示创建的帖子。

我强烈建议您看看下面的 StackOverflow 问题，以便更好地理解**请求之间的差异。发布[]** 和**请求。POST.get()**

<header>![](img/540c2ba90e5a347bd57c676bb96dfee0.png) [request.POST.get('sth') vs request.POST['sth'] - difference?](https://stackoverflow.com/questions/12518517/request-post-getsth-vs-request-poststh-difference) Sep 20 '12 Comments: 1 Answers: 3[![](img/83f13d376e6608cc602ae93b1cdbae4e.png)99![](img/fd423aaf5fec73c645f97544689ea934.png)](https://stackoverflow.com/questions/12518517/request-post-getsth-vs-request-poststh-difference) </header>

有什么区别

```
request.POST.get('sth') 
```

和

```
request.POST['sth'] 
```

没有找到类似的问题，两者对我来说是一样的，假设我可以分别使用它们，但也许我错了，这就是为什么我问。有什么想法吗？

[Open Full Question](https://stackoverflow.com/questions/12518517/request-post-getsth-vs-request-poststh-difference)

如果您的页面中有多个表单，您可以使用**动作**将它们分开，这样您的视图就不会同时收到多个请求。

为了在一个步骤中创建并保存一个对象，我们使用了 **create()** 方法。

现在让我们看看我们的 html 表单

```
<form method="POST" id="post-form">
  {% csrf_token %}
  <div class="form-group">
    <label>Title</label>
    <input type="text" class="form-control" id="title" placeholder="Title">
  </div>
   <div class="form-group">
     <label>Description</label>
     <textarea class="form-control" id="description" placeholder="Description"></textarea>
   </div>
   <button type="submit" class="btn btn-primary">Submit</button>
</form> 
```

Enter fullscreen mode Exit fullscreen mode

如你所知，我们使用 csrf_token 来发出 post 请求，这只是一个简单的引导表单。我们给每个输入一个 id，用 AJAX 通过 id 获取值。

```
$(document).on('submit', '#post-form',function(e){
    $.ajax({
        type:'POST',
        url:'{% url "create" %}',
        data:{
            title:$('#title').val(),
            description:$('#description').val(),
            csrfmiddlewaretoken:$('input[name=csrfmiddlewaretoken]').val(),
            action: 'post'
        },
        success:function(json){
            document.getElementById("post-form").reset();
            $(".posts").prepend('<div class="col-md-6">'+
                '<div class="row no-gutters border rounded overflow-hidden flex-md-row mb-4 shadow-sm h-md-250 position-relative">' +
                    '<div class="col p-4 d-flex flex-column position-static">' +
                        '<h3 class="mb-0">' + json.title + '</h3>' +
                        '<p class="mb-auto">' + json.description + '</p>' +
                    '</div>' +
                '</div>' +
            '</div>' 
            )
        },
        error : function(xhr,errmsg,err) {
        console.log(xhr.status + ": " + xhr.responseText); // provide a bit more info about the error to the console
    }
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

请注意，您必须将 jquery-2.2.4.min.js 包含到您的静态文件中，您可以从我的 [git 存储库](https://github.com/raszidzie/django-form-with-ajax)中获取它。

好吧！这是怎么回事？最初，向表单添加 id 属性很重要，这样 jQuery 就可以检测表单提交。AJAX **type** 指定 post 请求的类型，因为我们正在向数据库发送数据，而 **url** 指定发送请求的 url。然后，我们使用 val()方法通过 id 获取表单元素的值，并将其与指定要发送到服务器的数据的**数据**参数一起发送。必须获取 csrf_token 的值，否则将导致 403 禁止错误。如您所见，我们指定了一个动作，让视图检测哪个表单正在提交。

一旦请求成功发送，我们将清理表单，并将我们的新帖子添加到帖子行中。

> 的。prepend()方法将指定的内容作为 jQuery 集合中每个元素的第一个子元素插入(要将其作为最后一个子元素插入，请使用。append())。

如果发送请求时出现错误(我希望不是😅)然后最后一个函数会将这个错误信息添加到控制台，这样您就可以看到是什么导致了这个令人讨厌的错误。我猜你已经知道如何很好地向用户显示错误。

最后你会得到以下结果

[![](img/32cb86baa6fa41c36cab0e6be26baee6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Na9XwbDG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c1zafuarla7lhtg8a99g.gif)

#### 任务完成！

您刚刚学习了如何使用 AJAX 发布 Django 表单🚀🚀

你可以从我的 [git 库](https://github.com/raszidzie/django-form-with-ajax)克隆或下载这个项目，别忘了在 **twitter** 和 **instagram** 上关注和支持我，加入逆向宇航员社区！👨‍🚀下次教程见！

## # [姜戈](https://dev.to/t/django) <button name="button" type="button" data-info="{&quot;className&quot;:&quot;Tag&quot;,&quot;style&quot;:&quot;full&quot;,&quot;id&quot;:446,&quot;name&quot;:&quot;django&quot;}" class="crayons-btn follow-action-button whitespace-nowrap c-btn--secondary fs-base " aria-label="Follow tag: django" aria-pressed="false">跟随</button>

Django is a high-level Python Web framework that encourages rapid development and clean, pragmatic design. Built by experienced developers, it takes care of much of the hassle of Web development, so you can focus on writing your app without needing to reinvent the wheel. It’s free and open source.