# 代码和酒

> 原文：<https://dev.to/psychopanda/code-and-wine-1d1l>

# 简介

大家好，这是我的第一篇帖子，我想分享一件很酷的事情。
这不是指南或手册，(我从来不知道如何写它们)这是一个我喜欢的想法的概念，如果它能启发人，那将会很酷=)
所以...我喜欢葡萄酒。你可能知道，人们很容易迷失在各种各样的品种、混合、生产国家(每个国家都有很多地区)、收获年份等等之中，饮料的味道取决于所有这些和许多其他参数。当我喜欢酒的时候，我会记下他，以免忘记。
如果这些笔记一直在手边该有多好，怎么做呢？当然，写个应用=)
除了 wine，我也爱 django 和 django-rest-framework 在他们的帮助下，我们会实现想法。

# 任务

该应用程序是一个笔记系统，每个用户都可以创建、修改和删除作者的笔记。我们还不知道它会是一个网站还是一个移动客户端。数据库提供商没有发挥作用，因为它只是一个概念(我选择了 postgres)。作为一个附加功能，通过社交网络注册是提供的，通过社交网络共享您的笔记也是一样的。原则上，一切都很简单。

# 实现

github repo 上的完整代码:
[https://github.com/sixth-fox/vinecode](https://github.com/sixth-fox/vinecode)

我的要求. txt:
Django = = 2 . 2 . 3
djangorestframework = = 3 . 10 . 1
psycopg 2 = = 2 . 8 . 3
python-decouple = = 3.1
pytz = = 2019.1
SQL parse = = 0 . 3 . 0

顺便说一下，我习惯于编写从模型到路由器的代码，我知道有些人持有相反的观点。我决定把结构做得像品尝笔记，我必须经历很多选择，最后我停在了

```
class Note(models.Model):
    PERFECTLY = 5
    GOOD = 4
    SATISFACTORILY = 3
    BAD = 2
    ABOMINATION = 1
    SCORE_CHOICES = (
        (5, 'Excellent'),
        (4, 'Good'),
        (3, 'Satisfactory'),
        (2, 'Bad'),
        (1, 'Vinegar'),
    )
    date_create = models.DateTimeField(default=timezone.now)
    owner = models.ForeignKey('auth.User', on_delete=models.CASCADE, related_name='notes')
    slug = models.SlugField(max_length=150, unique_for_date='date_create', default='')
    date = models.DateField()
    wine = models.CharField(max_length=150)
    region = models.CharField(max_length=150)
    variety = models.CharField(max_length=150)
    country = models.CharField(max_length=150)
    vintage = models.DateField()
    alcohol = models.FloatField()
    price = models.PositiveSmallIntegerField()
    appearance = models.CharField(max_length=150)
    aromas = models.CharField(max_length=150)
    flavors = models.CharField(max_length=150)
    score = models.SmallIntegerField(choices=SCORE_CHOICES) 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，我有一个基本的用户模型，只要这样就够了。由于我们有嵌套的模型，在序列化器中重写创建和更新方法。

```
from django.contrib.auth.models import User
from rest_framework import serializers

from .models import Note

class UserSerializer(serializers.HyperlinkedModelSerializer):
    notes = serializers.HyperlinkedRelatedField(many=True,
                                                view_name='notes:note-detail',
                                                read_only=True)
    password = serializers.CharField(write_only=True)

    def create(self, validated_data):
        user = User(
            username=validated_data.get('username', None)
        )
        user.set_password(validated_data.get('password', None))
        user.save()
        return user

    def update(self, instance, validated_data):
        for field in validated_data:
            if field == 'password':
                instance.set_password(validated_data.get(field))
            else:
                instance.__setattr__(field, validated_data.get(field))
        instance.save()
        return instance

    class Meta:
        model = User
        fields = ('id', 'username', 'email', 'notes', 'password')
        extra_kwargs = {
            'url': {
                'view_name': 'notes:user-detail',
            }
        }

class NoteSerializer(serializers.HyperlinkedModelSerializer):
    owner = serializers.ReadOnlyField(source='owner.username')

    class Meta:
        model = Note
        exclude = ('date_create',)
        extra_kwargs = {
            'url': {
                'view_name': 'notes:note-detail',
            }
} 
```

Enter fullscreen mode Exit fullscreen mode

这个观点是实现了对泛型的继承，最有争议的一点是我没有使用路由器，这肯定不是很 pythonic 化，抱歉

```
notes/views.py

@api_view(['GET'])
def root_api(request, format=None):
    return Response({
        'users': reverse('notes:user-list', request=request, format=format),
        'notes': reverse('notes:note-list', request=request, format=format),
    })

class NoteList(generics.ListCreateAPIView):
    queryset = Note.objects.all()
    serializer_class = NoteSerializer
    permission_classes = (permissions.IsAuthenticatedOrReadOnly, IsOwnerOrReadOnly)

    def perform_create(self, serializer):
serializer.save(owner=self.request.user)

#more methods on github 
```

Enter fullscreen mode Exit fullscreen mode

在 permissions.py
中添加自定义权限

```
from rest_framework import permissions

class IsOwnerOrReadOnly(permissions.BasePermission):
    def has_object_permission(self, request, view, obj):
        if request.method == permissions.SAFE_METHODS:
            return True
return obj.owner == request.user 
```

Enter fullscreen mode Exit fullscreen mode

最后 urls.py

```
notes/urls.py

from django.urls import path
from rest_framework.urlpatterns import format_suffix_patterns

from . import views

app_name = 'notes'

urlpatterns = [
    path('', views.root_api, name='api-root'),
    path('note_list/', views.NoteList.as_view(), name='note-list'),
    path('note_detail/<int:pk>/', views.NoteDetail.as_view(), name='note-detail'),
    path('user_list', views.UserList.as_view(), name='user-list'),
    path('user_detail/<int:pk>/', views.UserDetail.as_view(), name='user-detail'),
]

urlpatterns = format_suffix_patterns(urlpatterns) 
```

Enter fullscreen mode Exit fullscreen mode