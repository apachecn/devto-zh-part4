# 将 Slack 和 Django 与 Active Directory 集成

> 原文：<https://dev.to/teaglebuilt/integrating-slack-with-django-with-active-directory-479p>

许多公司可能使用 Active Directory 进行域身份验证。LDAP，也称为“轻量级目录访问协议”，是应用程序用来与其他目录服务服务器通信的一种通信语言。

也就是说，我们将把两个外部 API 集成到应用程序中，python_ldap 和 SlackAPI。现在，在我们深入研究之前，我想澄清一下用例以及为什么这有意义。作为用户身份验证网关的公司域控制器也将在 Slack API 响应中作为键:值对列出。通常，您的 slack 工作区还将标识您公司域名。如果是这种情况，那么使用 Slack 作为 Active Directory 旁边的另一种形式的身份验证是有意义的，如果您计划将 Slack 集成到您的应用程序中的话！

至于与 Active Directory 的交互，幸运的是已经维护了一个库，它简化了与 python_ldap api 的交互。

因此，让我们首先设置对域控制器的身份验证。安装 django_auth_ldap 并配置您的 settings.py。

在此步骤中，您需要配置设置以连接到运行 Active Directory 的服务器，并配置您的设置以绑定、连接和声明如何在目录中搜索用户。

```
AUTH_LDAP_SERVER_URI = config("active_directory_server")
AUTH_LDAP_CONNECTION_OPTIONS = {
ldap.OPT_DEBUG_LEVEL: 1,
ldap.OPT_REFERRALS: 0
}
AUTH_LDAP_BIND_DN = config("DN")
AUTH_LDAP_BIND_PASSWORD = config("PASSWORD")
AUTH_LDAP_USER_SEARCH = LDAPSearch(
"CN=Users,DC=company,DC=test,DC=domain,DC=com", ldap.SCOPE_SUBTREE, "sAMAccountName=%(user)s"
)
AUTH_LDAP_USER_ATTR_MAP = {"first_name": "givenName", "last_name": "sn", "username": "sAMAccountName"}
LDAP_IGNORE_CERT_ERRORS = True
AUTHENTICATION_BACKENDS = (
'django_auth_ldap.backend.LDAPBackend',
) 
```

Enter fullscreen mode Exit fullscreen mode

值得一提的是，我取出了用于认证用户的默认 Django 后端“ModelBackend ”,并用..

```
django_auth_ldap.backend.LDAPBackend 
```

Enter fullscreen mode Exit fullscreen mode

配置好设置后，您应该能够使用 Django 的登录方法，使用您的凭据验证您的应用程序。说真的，就这么简单。

```
def login_view(request, *args, **kwargs):
    form = UserLoginForm(request.POST or None)
    try:
        kwargs.get("msg")
        if msg == True:
            messages.add_message(request, messages.SUCCESS, 'Your account has not yet been authenticated with Active Directory '
                                            'Please first register with the domain before using Slack.')
    except:
        pass

    if form.is_valid():
        user = form.login(request)
        if user is not None:
            login(request, user)
            return redirect("/")
    return render(request, "accounts/login.html", {"form": form}) 
```

Enter fullscreen mode Exit fullscreen mode

现在忽略 try / except 块，但是注意我是如何使用 django 表单之外的登录方法的，如果它有效的话。

```
class UserLoginForm(forms.Form):
    username = forms.CharField()
    password = forms.CharField(widget=forms.PasswordInput)

    def clean(self):
        username = self.cleaned_data.get('username')
        password = self.cleaned_data.get('password')
        user = authenticate(username=username, password=password, backend=LDAPBackend())
        if not user or not user.is_active:
            raise forms.ValidationError("Sorry, that login was invalid. Please try again.")
        return self.cleaned_data

    def login(self, request):
        username = self.cleaned_data.get('username')
        password = self.cleaned_data.get('password')
        user = authenticate(username=username, password=password, backend=LDAPBackend())
        return user 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，我将 Django 的 authenticate 方法设置为使用 LDAPBackend。

现在是有趣的时候了，与 Slacks API 交互，这是一个奇妙的体验，因为它有很棒的文档和结构。

因此，为了使用 Slack 进行身份验证，我们将使用 OAuth2.0 /使用 Slack 登录。如果您还没有创建您的 slack 应用程序，请这样做，并从 api 文档中获取您的 slack 登录按钮。看下面。

```
<a href="https://slack.com/oauth/authorize?scope=identity.basic&client_id=your_client_id"><img src="https://api.slack.com/img/sign_in_with_slack.png" /></a> 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/0d7d2fc61c93de5d78ea0233d76720b0.png)](https://slack.com/oauth/authorize?scope=identity.basic&client_id=your_client_id)

我选择在我的设置目录中的一个文件中设置 Slack API 凭证。

```
SLACK_CLIENT_ID = config("SLACK_CLIENT_ID")
SLACK_CLIENT_SECRET = config("SLACK_CLIENT_SECRET")
SLACK_AUTHORIZATION_URL = 'https://slack.com/oauth/authorize'
SLACK_OAUTH_ACCESS = 'https://slack.com/api/oauth.access'
SLACK_API_TOKEN = config("SLACK_API_TOKEN") 
```

Enter fullscreen mode Exit fullscreen mode

现在是时候考虑我们的授权流程了。在大多数情况下，Slack 将带您进入一个重定向页面，该页面允许用户使用他们的 Slack 凭证在工作空间中进行身份验证。所以我选择用 RedirectView /“基于类的视图”来处理这个问题。

在深入研究视图之前，让我们设置一下如何调用 API。

```
class SlackManager:

    def __init__(self):
        self.base_url = "https://slack.com/api/"

    def authenticate(self):
        data = {
            "client_id": settings.SLACK_CLIENT_ID,
            "scope": "identity.basic identity.team identity.avatar:read:user",
            "redirect_uri": "http://127.0.0.1:8000"
        }
        url = settings.SLACK_OAUTH_ACCESS
        response = requests.get(url, params=data)
        print(response)
        return response

    def get_user_data(self, token):
        endpoint = "users.identity"
        data = {
            "token": token
        }
        user_object = requests.get(self.base_url + endpoint, params=data)
        return user_object.json()

    def get_user_profile(self, user_id, token):
        data = {
            "token": token,
            "user": user_id
        }
        endpoint = "users.profile.get"
        response = requests.get(self.base_url + endpoint, params=data)
        print(response.text)
        return response.json() 
```

Enter fullscreen mode Exit fullscreen mode

如果您已经通读了 Slack 的 API 文档并创建了您的应用程序，那么您应该理解我们的请求中使用的参数。令牌是从“[https://slack.com/api/oauth.access](https://slack.com/api/oauth.access)”获得的访问令牌，范围定义了可用端点之间的权限。

现在，设置授权流是您的决定，但是当您为工作区创建和注册 slack 应用程序时，有一个重定向 uri 选项。这就是 slack 在处理完身份验证后知道将用户交付到哪里的方式。

下面是一个处理授权流逻辑的 RedirectView。

```
class OAuthView(RedirectView):
    permanent = True
    text_error = 'Something went wrong with updating your authorization.'

    def dispatch(self, request, *args, **kwargs):
        return super(OAuthView, self).dispatch(request, *args, **kwargs)

    def get(self, request, *args, **kwargs):
        code = request.GET.get('code')
        if not code:
            return self.auth_request()
        self.validate_state(request.GET.get('state'))
        access_content = self.oauth_access(code)
        if not access_content.status_code == 200:
            return self.error_message()
        api_data = access_content.json()
        if not api_data['ok']:
            return self.error_message(api_data['error'])
        user_obj = gather_slack_info(api_data)
        try:
            user = User.objects.get(username=user_obj["ldap_name"])
            if user is not None:
                profile = UserProfile.objects.get(user=user)    
                profile.slack_token = api_data["access_token"]
                profile.name = user_obj["name"]
                profile.email = user_obj["email"]
                profile.slack_id = user_obj["slack_id"]
                profile.extras = user_obj
                profile.save()
                login(request, user)
                messages.add_message(self.request, messages.SUCCESS, 'Your account has been successfully updated.')                                        
            return self.response()
        except ObjectDoesNotExist:
            return self.ldap_initial_auth(request)

    def auth_request(self):
        state = self.store_state()
        params = urlencode({
            'client_id': settings.SLACK_CLIENT_ID,
            'redirect_uri': self.request.build_absolute_uri(reverse('accounts:Oauth')),
            'scope': 'identity.basic identity.email identity.team identity.avatar',
            'state': state
        })
        return self.response(settings.SLACK_AUTHORIZATION_URL + '?' + params)

    def oauth_access(self, code):
        params = {
            'client_id': settings.SLACK_CLIENT_ID,
            'client_secret': settings.SLACK_CLIENT_SECRET,
            'code': code,
            'redirect_uri': self.request.build_absolute_uri(reverse('accounts:Oauth'))
        }
        return requests.get(settings.SLACK_OAUTH_ACCESS, params=params)

    def validate_state(self, state):
        state_before = self.request.session.pop('state')
        if state_before != state:
            raise StateMismatch('State mismatch upon authorization completion.'
                                ' Try new request.')
        return True

    def store_state(self):
        state = str(uuid.uuid4())[:6]
        self.request.session["state"] = state
        return state

    def response(self, redirect='/'):
        return HttpResponseRedirect(redirect)

    def ldap_initial_auth(self, request):
        msg = True
        return HttpResponseRedirect(reverse("accounts:login", kwargs={"msg": msg})) 
```

Enter fullscreen mode Exit fullscreen mode

让我们从 get 方法开始，它是将其他助手方法的所有功能结合在一起的主要方法。所以 slack 使用一个认证码来交换访问令牌。在[https://slack.com/api/authorize](https://slack.com/api/authorize)生成该代码，并在[https://slack.com/api/oauth.access](https://slack.com/api/oauth.access)交换访问代码。

state 参数应该用于避免伪造攻击，方法是传入一个值，该值对于您正在验证的用户是唯一的，并在验证完成时检查该值。由于这个原因，我们将状态存储在用户会话中。

如果我们的 API 调用成功，那么我们将提取我们想要的信息，并在一个字典中返回它。

```
def gather_slack_info(user_object):
   slack_user = user_object["user"]
   slack_team = user_object["team"]
   print(slack_user)
   ldap_name = ldapify_name(slack_user["name"])
   context = {
      "slack_id": slack_user["id"],
      "name": slack_user["name"],
      "ldap_name": ldap_name,
      "email": slack_user["email"],
      "team_id": slack_team["id"],
      "team_name": slack_team["name"],
      "domain": slack_team["domain"],
      "avatar": slack_user["image_24"]
    }
    return context 
```

Enter fullscreen mode Exit fullscreen mode

ldapify_name 将 slack 的用户名格式化，以匹配我们的 Active Directory 中用户名的格式。

```
def ldapify_name(name):
    x = name.lower()
    y = x.replace(" ", "")
    return y 
```

Enter fullscreen mode Exit fullscreen mode

在 RedirectView 的最后一个方法中，如果用户不在数据库中，我们会将他们送回登录屏幕。用户未在此应用程序中注册。因此，当您第一次使用您的凭据登录时，您将被添加为具有您的 active directory 用户名和密码的用户。

我认为每个用户第一次通过 LDAP 登录应用程序是很重要的，然后他们可能会在将来使用他们的工作空间作为一种额外的身份验证形式。

所以我将一个关键字参数传递给 login 视图，告诉 django 模板向用户显示一条消息，让他们第一次登录 Active Directory。