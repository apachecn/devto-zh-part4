# Github 页面上的博客 API

> 原文：<https://dev.to/thewhitewulfy/blog-api-on-github-pages-29ig>

我们将看到如何为 Jekyll 支持的静态网站构建一个 API，这样你就可以通过任何网络/移动应用程序使用静态数据。

我们还将看到如何使用 JSON 提要的 API。

## 步骤

### 输出数据为 JSON

如果您只需要将一些数据输出为 JSON，那么您可以使用`jsonify`过滤器轻松完成。

你的站点前端的所有变量都可以通过站点变量访问，所以我们使用 YAML 数组`site.whatever`并用`jsonify`液体过滤器将它转换成 JSON。

现在让我们尝试一个更实际的例子。如果您想在一个 JSON 文件中获得所有帖子的 JSON 输出，该怎么办？使用 Jekyll 也不难。您可以按照以下两个步骤来完成:

*   首先创建一个有名字的输出文件，姑且称之为`output.json`。

*   接下来打开`output.json`文件并添加以下代码:

```
 ---
  limit: 10
---

{%  for  post  in  site.posts  limit:  page.limit  %}
    {
         "id": "{{  post.id  }}",
         "date"     : "{{  post.date  |  date:  "%B %d, %Y"  }}",
         "title": {{  post.title  |  smartify  |  jsonify  }},
         "content_html": {{  post.content  |  jsonify  }},
         "url": "{{  site.url  }}{{  post.url  }}",

         "summary": {%  if  post.url  contains  "/blog/"  %}{{  post.excerpt  |  smartify  |  jsonify  }}{%  else  %}{{  post.description  |  smartify  |  jsonify  }}{%  endif  %},

         "date_published": "{{  post.date  }}",
         {%  if  post.categories  %} "categories"  : [
                {%  for  category  in  post.categories  %} "{{  category  }}"
                {%  if  forloop.last  %}{%  else  %},{%  endif  %}
                {%  endfor  %}
                ],
         {%  endif  %}
         {%  if  post.categories  ==  nil  %} "categories"  : [],  {%  endif  %}

         {%  if  post.tags  %} "tags"  : [
                {%  for  tag  in  post.tags  %} "{{  tag  }}"
                {%  if  forloop.last  %}{%  else  %},{%  endif  %}
                {%  endfor  %}
                ]
         {%  endif  %}
         {%  if  post.tags  ==  nil  %} "tags"  : []  {%  endif  %}

    }{%  unless  forloop.last  ==  true  %},{%  endunless  %}
{%  endfor  %} 
```

我们遍历了所有的`site.posts`并创建了一个 JSON 对象，每个 post 数据用逗号分隔。建好网站后，Jekyll 将负责解析 YAML 和液体数据，并以 JSON 格式输出带有帖子数据的`output.json`。

继续用 Jekyll 博客测试它，服务它或建立它，然后在你的`_site`文件夹中寻找你的`output.json`的文章数据。或者去`127.0.0.1:4000/output.json`看看结果。

如果您使用使用 github 页面，请访问您的博客链接`/output.json`并查看结果。

前面的`limit` meta 控制输出的帖子数量为`JSON`。

### 使用输出作为 API

很简单！将`output.json`放在一个名为`api`的新文件夹下，并重命名为`index.json`。

### 将此 API 作为 JSON feed

我阅读了关于 JSON 提要规范的文章，我对它的新颖性很感兴趣——与其说是 JSON 的易用性，不如说是使用 Atom XML 的难以克服的痛苦。

对于大多数开发人员来说，JSON 比 XML 更容易读写。开发人员可能会抱怨使用 XML 解析器，但是解码 JSON 通常只是一行代码。

因为我已经在我的博客上使用 Jekyll，所以编写代码需要在没有任何插件的情况下进行。

同样在这个例子中，我们已经准备好了一个 API，为什么不使用它呢？

```
 ---
layout: none
---
{
    ""version": "https://jsonfeed.org/version/1",
    "title": {{  site.name  |  smartify  |  jsonify  }},
    {%  if  site.description  %}"description": {{  site.description  |  smartify  |  jsonify  }},{%  endif  %}
    "home_page_url": "{{  site.url  }}/",
    "feed_url": "{{  site.url  }}/api/index.json",
    "icon": "{{  site.url  }}/assets/favicon/android-chrome-192x192.png",
    "favicon": "{{  site.url  }}/favicon.ico",
    "author": {
        "name": "{{  site.author  }}",
        "url": "https://alokprateek.in/",
        "avatar": "https://alokprateek.in/avatar3.jpg"
    },
    "expired": false,
    "items": [
           ]
} 
```

在`items`之下，以前存在于`index.json`中的东西进去了，没有了前面的物质，那就保持不变。

像`icon`、`favicon`和`author`这样的字段取决于您的特定实现，我不想通过使用前置变量来概括代码，而是直接粘贴值。

第二，您必须阅读规范，以获得关于哪些字段是可选的和强制的完整信息。

差不多就是这样！😊

最后一个例子，请看我的最终 JSON。而你可以在这个环节卷曲[blog.alokprateek.in/api/](https://blog.alokprateek.in/api/)

```
 ---
limit: 10
layout: none
---
{
    "version": "https://jsonfeed.org/version/1",
    "title": {{  site.name  |  smartify  |  jsonify  }},
    {%  if  site.description  %}"description": {{  site.description  |  smartify  |  jsonify  }},{%  endif  %}
    "home_page_url": "{{  site.url  }}/",
    "feed_url": "{{  site.url  }}/api/index.json",
    "icon": "{{  site.url  }}/assets/favicon/android-chrome-192x192.png",
    "favicon": "{{  site.url  }}/favicon.ico",
    "author": {
        "name": "{{  site.author  }}",
        "url": "https://alokprateek.in/",
        "avatar": "https://alokprateek.in/avatar3.jpg"
    },
    "expired": false,
    "items": [
{%  for  post  in  site.posts  limit:  page.limit  %}
    {
      "title": "{{  post.title  }}",
      "date"     : "{{  post.date  |  date:  "%B %d, %Y"  }}",
      "url": "{{  site.url  }}{{  post.url  }}",
      "summary": {{  post.excerpt  |  smartify  |  jsonify  }},
      "content_html": {{  post.content  |  jsonify  }},
      {%  if  post.categories  %} "categories"  : [
        {%  for  category  in  post.categories  %} "{{  category  }}"
        {%  if  forloop.last  %}{%  else  %},{%  endif  %}
        {%  endfor  %}
        ],
      {%  endif  %}
      {%  if  post.categories  ==  nil  %} "categories"  : [],  {%  endif  %}
      {%  if  post.tags  %} "tags"  : [
        {%  for  tag  in  post.tags  %} "{{  tag  }}"
        {%  if  forloop.last  %}{%  else  %},{%  endif  %}
        {%  endfor  %}
        ]
      {%  endif  %}
      {%  if  post.tags  ==  nil  %} "tags"  : []  {%  endif  %}

    }
    {%  unless  forloop.last  %},{%  endunless  %}
{%  endfor  %}
    ]
} 
```

下次见！

干杯！🍻