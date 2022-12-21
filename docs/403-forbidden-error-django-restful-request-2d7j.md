# 403 禁止错误 Django restful 请求。

> 原文：<https://dev.to/suryanarayana/403-forbidden-error-django-restful-request-2d7j>

在每个视图函数定义(views.py)的顶部添加以下语句。

```
@api_view(["POST", "GET"]) 
```

例:

```
@api_view(["POST", "GET"])
def GivenInput():
   return Response(e.args[0],status.HTTP_400_BAD_REQUEST) 
```

注*:我不知道有什么替代方法可以让它在整个文件中是全局的。