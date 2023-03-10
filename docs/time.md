# 将 html <time>元素与 Angular 一起使用</time>

> 原文：<https://dev.to/leonelngande/using-the-html-time-time-element-with-angular-4437>

> `<time [dateTime]="'2019-08-09 16:22:20'">8/9/2019</time>`

所以今天不得不在 Angular 组件模板中使用 html 时间元素，并遇到了以下错误:

`ERROR Error: Uncaught (in promise): Error: Template parse errors:`

`Can't bind to 'datetime' since it isn't a known property of 'time'.`

下面是我的模板的样子:

```
<time [datetime]="comment.updated_at">
  {{ comment.updated_at }}
</time> 
```

我很困惑，我在 [Mozilla Developer Network](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/time) 上确认了时间元素有一个 datetime 属性。进一步的调查把我带到了[角库中的这个问题](https://github.com/angular/angular/issues/26255)。

阿列克谢·祖耶夫解释道:

我认为 Angular 恰当地使用了时间标签。

`<time>`具有日期时间属性的标记应该类似于:

```
<time [attr.datetime]="'2019-08-09 16:22:20'">4:22:20 PM</time> 
```

而带有日期时间属性的<time>标签为:</time>

```
<time [dateTime]="'2019-08-09 16:22:20'">4:22:20 PM</time> 
```

因此，这里的关键点是属性名和属性名之间的差异，如 Pawel Kozlowski 所示(对上述问题的评论):

*   属性名为`datetime`:[https://developer . Mozilla . org/en-US/docs/Web/HTML/Element/time](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/time)
*   属性名(Angular 中的默认绑定)是`dateTime`:[https://developer . Mozilla . org/en-US/docs/Web/API/HTMLTimeElement](https://developer.mozilla.org/en-US/docs/Web/API/HTMLTimeElement)

所以这是我的模板最后的样子:

```
<time [dateTime]="comment.updated_at">
  {{ comment.updated_at }}
</time> 
```