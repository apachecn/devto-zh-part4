# 使用电子邮件验证的用户注册

> 原文：<https://dev.to/shafikshaon/user-registration-with-email-verification-58p3>

假设您有一个名为 **user_registration** 的项目，它包含一个名为 **accounts** 的应用程序。

首先，我们需要配置我们的电子邮件服务器。所以，进入`user_registration/setting.py`并写下以下几行:

```
 EMAIL_BACKEND = 'django.core.mail.backends.smtp.EmailBackend'  
    MAILER_EMAIL_BACKEND = EMAIL_BACKEND  
    EMAIL_HOST = 'your_mail_server'  
    EMAIL_HOST_PASSWORD = 'your_password'  
    EMAIL_HOST_USER = 'your_email'  
    EMAIL_PORT = 465  
    EMAIL_USE_SSL = True  
    DEFAULT_FROM_EMAIL = EMAIL_HOST_USER 
```

Enter fullscreen mode Exit fullscreen mode

为了测试上述配置工作，打开终端并导航到`user_registration`项目，然后运行以下命令:

```
 $ python manage.py shell
    >>> from django.core.mail import send_mail
    >>> send_mail( 'Subject here', 'Here is the message.', 'me@shafikshaon.com', ['shafikshaon@gmail.com'], fail_silently=False, ) 
```

Enter fullscreen mode Exit fullscreen mode

它将返回 1 作为状态代码。然后检查你的邮件，会有邮件到达。

现在在`accounts/templates/accounts/signup.html`中创建一个表单，并编写下面的代码:

```
 <!DOCTYPE html>  
    <html lang="en">  
    <head>  
        Signup  
        <meta charset="utf-8">  
        <meta name="viewport" content="width=device-width, initial-scale=1">  
        <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.1.3/css/bootstrap.min.css">  
    </head>  
    <body>  

    <div class="container" style="margin-top: 50px;">  
        <div class="row justify-content-center">  
            <div class="col-md-5 shadow-sm p-3 mb-5 bg-white rounded">  
                <h2>Signup</h2>  
                <form method="post">  
                    {% csrf_token %}  
                    <div class="form-group">  
                        <label for="first_name">First Name:</label>  
                        <input type="text" class="form-control" id="first_name" placeholder="Enter first name"  
      name="first_name">  
                    </div>  
                    <div class="form-group">  
                        <label for="last_name">Last Name:</label>  
                        <input type="text" class="form-control" id="last_name" placeholder="Enter last name"  
      name="last_name">  
                    </div>  
                    <div class="form-group">  
                        <label for="username">Username:</label>  
                        <input type="text" class="form-control" id="username" placeholder="Enter username" name="username">  
                    </div>  
                    <div class="form-group">  
                        <label for="email">Email:</label>  
                        <input type="email" class="form-control" id="email" placeholder="Enter email" name="email">  
                    </div>  
                    <div class="form-group">  
                        <label for="pwd1">Password:</label>  
                        <input type="password" class="form-control" id="pwd1" placeholder="Enter password" name="password1">  
                    </div>  
                    <div class="form-group">  
                        <label for="pwd2">Confirm Password:</label>  
                        <input type="password" class="form-control" id="pwd2" placeholder="Reenter password"  
      name="password2">  
                    </div>  
                    <button type="submit" class="btn btn-primary">Submit</button>  
                </form>  
            </div>  
        </div>  
    </div>  

    </body>  
    </html> 
```

Enter fullscreen mode Exit fullscreen mode

现在在`accounts/templates/accounts/acc_active_email.html`中创建一个表单，并编写下面的代码:

```
 {% autoescape off %}  
    Hi {{ user.username }},  
    Please click on the link to confirm your registration,  

    http://{{ domain }}{% url 'activate' uidb64=uid token=token %}  
    If you think, it's not you, then just ignore this email.  
    {% endautoescape %} 
```

Enter fullscreen mode Exit fullscreen mode

现在打开`accounts/urls.py`，写下下面的路径:

```
 from django.urls import path  
    from accounts import views  

    urlpatterns = [  
        path('signup/', views.signup, name="signup"),  
        path('activate/<uidb64>/<token>/',views.activate, name='activate'),  
    ] 
```

Enter fullscreen mode Exit fullscreen mode

现在打开`accounts/tokens.py`并编写下面的代码:

```
 from django.contrib.auth.tokens import PasswordResetTokenGenerator  
    from django.utils import six  

    class AccountActivationTokenGenerator(PasswordResetTokenGenerator):  
        def _make_hash_value(self, user, timestamp):  
            return (  
                six.text_type(user.pk) + six.text_type(timestamp) +  
                six.text_type(user.is_active)  
            )  

    account_activation_token = AccountActivationTokenGenerator() 
```

Enter fullscreen mode Exit fullscreen mode

现在打开`accounts/forms.py`并编写下面的代码:

```
 from django.contrib.auth.forms import UserCreationForm  
    from django.contrib.auth.models import User  

    class SignUpForm(UserCreationForm):  
        class Meta:  
            model = User  
            fields = ('email', 'first_name', 'last_name', 'username') 
```

Enter fullscreen mode Exit fullscreen mode

现在打开`accounts/views.py`并编写下面的代码:

```
 from django.http import HttpResponse  
    from django.shortcuts import render  
    from django.contrib.sites.shortcuts import get_current_site  
    from django.utils.encoding import force_bytes, force_text  
    from django.utils.http import urlsafe_base64_encode, urlsafe_base64_decode  
    from django.template.loader import render_to_string  

    from .forms import SignUpForm  
    from .tokens import account_activation_token  
    from django.contrib.auth.models import User  
    from django.core.mail import EmailMessage  

    def signup(request):  
        if request.method == 'GET':  
            return render(request, 'accounts/signup.html')  
        if request.method == 'POST':  
            form = SignUpForm(request.POST)  
            # print(form.errors.as_data())  
            if form.is_valid():  
                user = form.save(commit=False)  
                user.is_active = False  
                user.save()  
                current_site = get_current_site(request)  
                mail_subject = 'Activate your account.'  
                message = render_to_string('accounts/acc_active_email.html', {  
                    'user': user,  
                    'domain': current_site.domain,  
                    'uid': urlsafe_base64_encode(force_bytes(user.id)).decode(),  
                    'token': account_activation_token.make_token(user),  
                })  
                to_email = form.cleaned_data.get('email')  
                email = EmailMessage(  
                    mail_subject, message, to=[to_email]  
                )  
                email.send()  
                return HttpResponse('Please confirm your email address to complete the registration')  
         else:  
            form = SignUpForm()  
         return render(request, 'accounts/signup.html', {'form': form})  

    def activate(request, uidb64, token):  
        try:  
            uid = force_text(urlsafe_base64_decode(uidb64))  
            user = User.objects.get(id=uid)  
        except(TypeError, ValueError, OverflowError, User.DoesNotExist):  
            user = None  
        if user is not None and account_activation_token.check_token(user, token):  
            user.is_active = True  
            user.save()  
            return HttpResponse('Thank you for your email confirmation. Now you can login your account.')  
        else:  
            return HttpResponse('Activation link is invalid!') 
```

Enter fullscreen mode Exit fullscreen mode

当用户注册时，`is_active`设置为`False`，这意味着用户在注册过程中设置为不活动。
通过`render_to_string()`方法，通过上下文发送数据/值。这里，数据/值发送`accounts/acc_active_email.html`和`user`、`domain`、`uid`和`token`数据作为上下文数据。

将`uid`转换成文本。然后，通过`uid`找到用户对象。
现在检查`account_activation_token.check_token(user, token)`以检查这是否与之前创建的令牌匹配。如果匹配，将`user.is_active`设置为`True`。