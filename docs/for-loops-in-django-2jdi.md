# 对于 Django 的循环

> 原文：<https://dev.to/anuragrana/for-loops-in-django-2jdi>

最初发表于[pythoncircle.com](https://www.pythoncircle.com/post/685/for-loop-in-django-template/)

下面使用的代码演示:[DjangoTemplateFiddle.com](https://www.djangotemplatefiddle.com/f/CftUNu/)

For 循环用于迭代任何可迭代对象，一次访问一项，并使其在 for 循环体内可用。

例如，如果您想在 Django 模板中创建一个国家下拉列表，您可以使用下面的代码。

```
{% for country in country_list %}
    <option name="{{country}}">{{country|title}}</option>
{% endfor %} 
```

就像在 Python 中一样，使用下面的代码来迭代人们的姓名和年龄的字典。

```
{% for name, age in data.items %}
    Name: {{name}}, Age: {{age}} <br>
{% endfor %} 
```

### [T1】检查 for 循环中使用的 iterable 是否为空:](#checking-if-iterable-used-in-for-loop-is-empty)

假设您想要向登录用户显示新消息。您从数据库中获取所有新消息，并将它们存储在一个列表中，并与模板一起传递给呈现函数。

现在，您可以检查消息列表是否为空，然后相应地显示消息。示例:

```
{% if messages %}
    {% for message in messages %}
        {{ message }}<br>
    {% endfor %}
{% else %}
    <div>No new message for you</div>
{% endif %} 
```

或者您可以使用`{% empty %}`标签和`{% for %}`标签，如下所示。

```
{% for message in messages %}
    {{ message }}
{% empty %}
    <div>No new message for you</div>
{% endfor %} 
```

### 在 Django 中断开 for 循环:

这对你来说可能是个坏消息。Django 模板中没有用于循环的 break 语句。

根据您的要求，您可以执行以下操作之一。

**选项 1** -遍历整个列表，但如果条件不匹配，则不执行任何操作。

例如，您正在打印列表中的数字，并且需要在遇到数字 99 时立即退出列表。通常情况下，这将在 Python 中完成，如下所示。

```
for number in numbers:
    if 99 == number:
        break
    print(number) 
```

但是 Django 模板中没有用于循环的 break 语句。可以实现和下面(几乎)一样的功能。

```
{% set isBreak = False %}
{% for number in numbers %}
    {% if 99 == number %}
        {% set isBreak = true %}
    {% endif %}

    {% if isBreak %}
        {# this is a comment. Do nothing. #}
    {% else %}
        <div>{{number}}</div>
    {% endif %}
{% endfor %} 
```

**选项 2**——你可以创建自己的[自定义模板标签](https://www.pythoncircle.com/post/42/creating-custom-template-tags-in-django/)。

你可以用 Django 模板进行实验，也可以创建自己的小提琴并与他人分享。 [Django 模板小提琴](https://www.djangotemplatefiddle.com/)。