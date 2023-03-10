# 笑博客教程第 3 部分-在 Django 中发送替代内容类型的电子邮件

> 原文：<https://dev.to/cindyachieng/laughing-blog-tutorial-part-3-send-alternative-content-type-emails-in-django-4le2>

Html 邮件可以很好地格式化，包括图片，不像纯文本邮件。然而，一些电子邮件客户端(大部分是旧的)不能读取 html。因此，在发送`html email`的同时发送`plain text`封电子邮件可能是有用的，因为我们希望所有的订户都能收到他们的电子邮件。

为了实现发送多个电子邮件内容，我们将使用名为`EmailMultiAlternatives`的 Django 电子邮件库。为了获得更简洁的代码，我们将创建一个函数。

我们还将介绍 django `message framework`来显示用户通知，而不是将通知打印到控制台[见文档](https://docs.djangoproject.com/en/2.0/ref/contrib/messages/)

我们开始吧！

在简讯 app 上新建一个文件，命名为`emails.py`。

```
def send_multiple_email(name, receiver):
    subject = 'Welcome to Laughing Blog Tutorial'
    sender = 'test@achiengcindy.com'
    text_template = render_to_string(
        'newsletter/email-subscribe.txt', {'name': name})
    html_template = render_to_string(
        'newsletter/email-subscribe.html', {'name': name})
    message = EmailMultiAlternatives(
        subject, text_template, sender, [receiver])
    message.attach_alternative(html_template, 'text/html')
    message.send() 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，我们创建了一个带两个参数的函数`send_multiple_email`。定义电子邮件的主题和发件人。使用 render_to_string 函数并传递电子邮件正文的模板。

我们现在可以创建电子邮件模板了

**简讯/模板/email-subscribe.html**

```
<h2>Hello {{name}}</h2> 
Welcome to Laughing Blog Newsletter and thank you for signing up. 
You will find the most exciting news about Python, Django, Programming tips and web development tools that will go along way to see you grow your skills 
```

Enter fullscreen mode Exit fullscreen mode

**简讯/模板/email-subscribe.txt**

```
Hello {{name}}
Welcome to Laughing Blog  Newsletter and thank you for signing up. 
You will find the most exciting news about Python, Django,Programming tips and web development tools that will go along way to see you grow your skills 
```

Enter fullscreen mode Exit fullscreen mode

现在是对视图进行一些更改的好时机。py .将您的视图更改如下:

### 简讯/views.py

```
def newsletter_subscribe(request):
    if request.method == 'POST':
        form = NewsUserForm(request.POST)
        if form.is_valid():
            instance = form.save(commit=False)
            # we dont want to save just yet
            if NewsUsers.objects.filter(email=instance.email).exists():
                print('your email Already exists in our database')
            else:
                instance.save()
                print('your email has been submitted to our database')
                send_multiple_email(instance.name, instance.email)
    else:
        form = NewsUserForm()
    context = {'form': form}
    template = 'newsletter/subscribe.html'
    return render(request, template, context) 
```

Enter fullscreen mode Exit fullscreen mode

首先，导入我们刚刚创建的函数

```
from .emails import send_multiple_email
```

Then instead of `send_mail` call `send_multiple_email`
Run Server and test

### Django 消息框架

使用以下内容编辑您的视图:
第一次导入`messages framework`

```
from django.contrib import messages 
```

and next replace these line of codes

```
print('your email Already exists in our database') 
print('your email has been submitted to our database') 
```

Enter fullscreen mode Exit fullscreen mode

分别用这些线

```
 messages.warning(request, 'your Email Already exists in our database') 
 messages.success(request, 'your Email has been submitted to our database') 
```

Enter fullscreen mode Exit fullscreen mode

要显示消息，请编辑订阅模板，如下所示:

```
<ul class="messages">
{% for message in messages %}
<li{% if message.tags %} class="{{ message.tags }}"{% endif    %}>{{ message }</li>
{% endfor %}
</ul>
{% else %}
<form action="{% url 'newsletter:subscribe' %}" method="post">
{% csrf_token %}
{{ form.as_p }}
<button type="submit">Submit</button>
</form>
{% endif %} 
```

Enter fullscreen mode Exit fullscreen mode

### 结论

我们现在能够发送替代电子邮件，并有一个更可读的代码。本教程的源代码可以在 [Github](https://github.com/achiengcindy/laughing_blog) 上获得。[在推特上联系我](https://twitter.com/achiengcindy_)

这篇文章最初发表在[achiengcindy.com](https://achiengcindy.com/blog/2018/03/23/sending-emails-django-application-using-mailjet/)