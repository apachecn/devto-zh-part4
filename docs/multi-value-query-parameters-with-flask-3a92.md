# 使用 Flask 的多值查询参数

> 原文：<https://dev.to/svencowart/multi-value-query-parameters-with-flask-3a92>

## 超短烧瓶简介

Flask 是一个用于构建 Python 应用程序的微框架。它可以用来构建 RESTful APIs。

### 诬陷问题

通常，RESTful APIs 有获取文档的 GET 路径。例如，`GET http://localhost:5000/users`将在其响应中返回所有用户文档的列表。过滤该请求结果的一般惯例是附加一个查询字符串，如`GET http://localhost:5000/users?type=jedi&species=human`。这个请求将返回所有绝地和人类类型的用户。此外，单个查询参数可以有多个值，如`GET http://localhost:5000/users?type=jedi&type=sith&species=human`。该请求将返回所有绝地或西斯和人类用户。(向单个查询参数提供多个值的其他格式在实践中也有使用，但 Flask 不支持。其中一种格式是`/users?type=jedi,sith`。)使用 Flask，通过访问路由定义中的`request.args`属性来检索查询参数:

```
@app.route('/users')
def fetch_users():
    query_params = request.args
    .
    .
    . 
```

Enter fullscreen mode Exit fullscreen mode

### 问题

如果查询字符串有类似`?type=jedi&type=sith&species=human`的多值参数，那么`request.args.to_dict()`返回的值是:

```
{  "type":  "jedi",  "species":  "human"  } 
```

Enter fullscreen mode Exit fullscreen mode

等等，type 的第二个值怎么了？默认情况下，Flask 假定查询参数是单值的。

### 解

默认行为可以像`request.args.to_dict(flat=False)`一样通过将`flat=False`传递给`to_dict()`来修改。这会以列表形式返回每个参数的所有值，而不是只返回第一个值。不幸的是，只有一个值的参数也应用了这种行为，这意味着上面例子的输出是:

```
{  "type":  ["jedi",  "sith"],  "species":  ["human"]  } 
```

Enter fullscreen mode Exit fullscreen mode

此时，每个参数的所有值都是可访问的，但是如果只有一个值的参数不被转换为列表，这将会很方便。我们可以用下面的代码实现这种行为:

(注意，为了简洁起见，有意省略了 Flask 实现的某些部分。)

```
from flask import Flask, request

def normalize_query_param(value):
    """
    Given a non-flattened query parameter value,
    and if the value is a list only containing 1 item,
    then the value is flattened.

    :param value: a value from a query parameter
    :return: a normalized query parameter value
    """
    return value if len(value) > 1 else value[0]

def normalize_query(params):
    """
    Converts query parameters from only containing one value for each parameter,
    to include parameters with multiple values as lists.

    :param params: a flask query parameters data structure
    :return: a dict of normalized query parameters
    """
    params_non_flat = params.to_dict(flat=False)
    return {k: normalize_query_param(v) for k, v in params_non_flat.items()}

@app.route('/users')
def fetch_users():
    query_params = normalize_query(request.args)
    .
    .
    . 
```

Enter fullscreen mode Exit fullscreen mode

这里，使用`request.args.to_dict(flat=False)`检索查询参数，然后我们遍历每个参数。如果参数值包含多个值，则将未修改的列表返回给参数，否则，返回其单个值。以下查询字符串`?type=jedi&type=sith&species=human`的结果将是:

```
{  "type":  ["jedi",  "sith"],  "species":  "human"  } 
```

Enter fullscreen mode Exit fullscreen mode

现在，所有多值查询参数都由列表表示，同时保持单值查询参数的预期原始行为。

我希望你学到了一些东西。请在评论中告诉我你的想法。

普罗斯特。