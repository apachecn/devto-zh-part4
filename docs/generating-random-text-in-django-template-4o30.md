# 在 Django 模板中生成随机文本

> 原文：<https://dev.to/anuragrana/generating-random-text-in-django-template-4o30>

**试玩:[https://www.djangotemplatefiddle.com/f/wV5xSd/](https://www.djangotemplatefiddle.com/f/wV5xSd/)T3】**

我们可以在 Django 模板中生成随机文本。这用于填充样本数据。

Django 为此提供了内置的模板标签`{% lorem %}`。

要用 lorem ipsum 文本生成一个段落，使用上面的标签
。

```
<!-- Lorem ipsum paramgraph -->
{% lorem %} 
```

**输出:**

```
<!-- Lorem ipsum paramgraph -->
Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum. 
```

要生成包含随机文本的两个段落，请使用带参数的标签。

```
<!-- random text paragraph -->
{% lorem 2 b %} 
```

**输出:**

```
<!-- random text paragraph -->
Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.

Deserunt quisquam dolores minus tempore aperiam itaque minima maxime, atque aperiam libero recusandae quod aliquid sed quo a deserunt, a at rem? Incidunt aut quibusdam est distinctio amet nemo, beatae dolorum fugit corporis recusandae dolorem praesentium vel obcaecati consectetur, voluptas quisquam a neque atque debitis, tenetur eaque nostrum ex? 
```

要生成几个随机单词，请使用带有以下参数的标签:

```
<!-- random words of length 10 -->
{% lorem 10 w random %} 
```

**输出:**

```
<!-- random words of length 10 -->
autem veritatis quisquam optio quibusdam non qui assumenda dolores alias 
```

你可以在这里看到(并实验)演示:[https://www.djangotemplatefiddle.com/f/wV5xSd/](https://www.djangotemplatefiddle.com/f/wV5xSd/)