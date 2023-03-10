# 从 jquery-ujs 迁移到 rails-ujs

> 原文：<https://dev.to/nejremeslnici/migrating-from-jquery-ujs-to-rails-ujs-k9m>

如果你维护一个重要的 Ruby on Rails 应用程序，你可能已经使用了一些不显眼的 JavaScript 程序，或者 T2 UJS 程序。一般来说，UJS 是一种*而不是*将 JS 代码内联到 HTML 标记中的技术，但在 Rails 环境中，它是一个由 Rails 团队发明的小型 JavaScript 库，让您在一些常见情况下几乎不用编写任何代码。该库支持:

*   链接或表单中的确认对话框
*   链接操作中的非 GET 方法
*   自动禁用表单中的按钮以防止重复提交
*   远程表单和链接-自动 AJAX 调用和响应处理
*   AJAX 请求中的自动 CSRF 保护

UJS 库最初是在 2010 年左右使用 jQuery 编写的，名字是 **[`jquery-ujs`](https://github.com/rails/jquery-ujs)** 。2016 年，Rails 团队 **[决定](https://github.com/rails/rails/issues/25208)放弃 jQuery 依赖**，将库重写为 vanilla-JS 一个，命名为 **[`rails-ujs`](https://github.com/rails/rails-ujs)** 。此后不久，从 Rails 5.1 开始，`rails-ujs`库已经被[移入 Rails](https://github.com/rails/rails/tree/master/actionview/app/assets/javascripts) 本身。

### 为什么要迁移？

我可以看到一些可能让你想换成`rails-ujs`的原因:

*   您可能希望将这次迁移作为从项目中完全移除 jQuery 依赖项的起点。并不是说我反对 jQuery，但是很明显，它的主要目的之一——在一个 API 下统一浏览器行为——已经不像几年前那么重要了，因为当今的现代浏览器 API 趋向于融合和标准化。
*   您将 Rails 应用程序升级到了 Rails 5.1 或更高版本，并且只想使用官方支持的作为 Rails 框架一部分的 UJS。在`rails-ujs`代码看似 **[积极开发](https://github.com/rails/rails/pulls?q=is%3Apr+rails-ujs+is%3Aclosed)维护**的同时，原来的`jquery-ujs`库正在慢慢落后。
*   **你想用[刺激](https://stimulusjs.org/)** 来[响应你的远程形式](https://medium.com/parallel-thinking/stimulus-rails-remote-forms-ca5b3e2f02ed)或其他 UJS 动作。如果你尝试这样做，你会注意到 Stimulus 不与`jquery-ujs`一起工作，但它与`rails-ujs`一起工作，因为它需要本地 DOM 事件，而这些事件只有[和`rails-ujs`才能触发](https://github.com/stimulusjs/stimulus/issues/78)。
*   与作为[单文件库](https://github.com/rails/jquery-ujs/tree/master/src)的`jquery-ujs`不同，当前的`rails-ujs`是以[更模块化的风格](https://github.com/rails/rails/tree/master/actionview/app/assets/javascripts/rails-ujs/features)编写的，因此**对你来说阅读源代码**或投稿可能更容易，尤其是如果你熟悉 CoffeeScript 的话(不过我猜测 UJS 最终会将[重写为](https://github.com/rails/rails/issues/25208#issuecomment-225033449)到 ES6)。

对于我们在 NejRemeslnici 的团队来说，所有四点都是相关的，但特别是第二点让我们在最近将我们的应用从 Rails 4.2 升级到 6.0 后接触了代码。我们希望更接近我们所使用的依赖项的主动开发！也被说服了？请继续阅读！

### 先简单的事情

如果你仍然通过资产管道打包你的 JS 代码，**只需交换相应资产文件中的库**(否则参见中关于 webpacker 的配置):

```
// app/assets/javascripts/application.js
-//= require jquery_ujs
+//= require rails-ujs 
```

Enter fullscreen mode Exit fullscreen mode

> 请注意，在 `jquery`之后，您仍然需要库*，这样，即使在使用 [`$.ajax()` jQuery 方法](https://api.jquery.com/jquery.ajax/)进行手工 AJAX 调用时，自动 CSRF 保护仍然有效。*

接下来，在代码中搜索`$.rails.<something>`，并将所有出现的内容替换为`Rails.<something>`。如果您以某种方式定制了 UJS 库，比如当您使用定制的确认对话框或类似的东西时，这是相关的。

> 从技术上来说，您并不是被迫进行这种替换，因为`rails-ujs`认识到您仍然在使用 jQuery，并且[为您设置了](https://github.com/rails/rails/blob/master/actionview/app/assets/javascripts/rails-ujs/start.coffee#L15)和`$.rails = Rails`链接。但是，为什么要将它与普通的 JS 库一起使用呢？

另外，如果您愿意的话，现在是在您的 UJS 定制化代码中去掉 jQuery 内容的好时机。

### 处理 AJAX 事件处理程序

这就是迁移开始有点棘手的地方…

#### 新增事件参数语法

AJAX 事件处理程序的附加参数的语法在`rails-ujs`中有了很大的改变。事件本身现在被实现为一个 [`CustomEvent`](https://developer.mozilla.org/en-US/docs/Web/API/CustomEvent/CustomEvent) ，所有额外的参数(T2、`status`和`xhr`)以前作为单独的参数被发送给处理程序，现在**被捆绑到`event.detail`属性**中(作为一个数组)。所以不用:

```
$(document).on("ajax:success", function(event, data, status, xhr) {
  ...
}); 
```

Enter fullscreen mode Exit fullscreen mode

你应该这样写:

```
$(document).on("ajax:success", function(event) {
  var data = event.detail[0];
  var status = event.detail[1];
  var xhr = event.detail[2];
  ...
}); 
```

Enter fullscreen mode Exit fullscreen mode

每个特定事件类型的`event.detail`的内容在[官方指南](https://guides.rubyonrails.org/working_with_javascript_in_rails.html#rails-ujs-event-handlers)中有详细的记录。

#### 将 jQuery 事件迁移到 UJS 事件

如果您混合处理 [jQuery AJAX 事件](https://api.jquery.com/Ajax_Events/)(如`ajaxSuccess`、`ajaxComplete`等)，可能会让您大吃一惊。)用 UJS AJAX 调用你的代码。以前，你可能交替使用 jQuery 事件(例如`ajaxSuccess`)和 UJS 事件(例如`ajax:success`)的处理程序，因为 jQuery 总是在幕后使用，因此**这两个事件总是被触发**。但是这不再是真的，你必须**将所有的 jQuery 事件转换成相应的 UJS 事件**。

> 当然，这只适用于源自 UJS 的 AJAX 调用，比如在表单中指定`remote: true`。如果您使用通过 jQuery `$.ajax()`方法进行的定制 AJAX 调用，您仍然应该处理 jQuery 事件，比如`ajaxSuccess`。

因此，例如，`ajaxSuccess` jQuery 事件处理程序可以重写为 UJS 变体，如下所示:

```
// old form (jQuery event)
$(document).ajaxSuccess(function(event, xhr, options, data) {
  ...
}

// ⟶ new form (UJS event)
$(document).on("ajax:success", function(event) {
  ...
} 
```

Enter fullscreen mode Exit fullscreen mode

注意，jQuery 事件使用了*另外一组*额外的参数，所以请仔细重写它们，如果不确定，请检查 [jQuery 文档](https://api.jquery.com/ajaxSuccess/#ajaxSuccess-handler)。

#### 注意合适的 AJAX 数据类型

在`jquery-ujs`中，AJAX 调用响应主体没有以任何方式进行处理，它只是被传递给适当的事件处理程序。`rails-ujs`然而， **[尝试](https://github.com/rails/rails/blob/master/actionview/app/assets/javascripts/rails-ujs/utils/ajax.coffee#L63)做一些基本的响应处理**:

*   它将 JSON 响应解析成 JavaScript 对象
*   它将 HTML / XML / SVG 响应解析成一个`Document`对象
*   最重要的是，它**自动执行 JavaScript** 响应。

尤其是第三个选项，如果您对 AJAX 数据类型不够小心，可能会导致意外的行为。默认情况下，`rails-ujs`将 AJAX 调用的数据类型设置为`"script"`，这告诉**期待来自服务器**的 JavaScript 响应，并在收到响应时执行响应。

现在，如果您的服务器试图发送 JavaScript 之外的任何东西(例如，一些 HTML 内容)，AJAX 将突然失败，因为 UJS 库试图执行它期望的 JavaScript。要解决这个问题，要么**确保服务器返回正确的数据类型**(可能是[服务器生成的 Javascript 响应](https://signalvnoise.com/posts/3697-server-generated-javascript-responses)，SJR)要么**通过 [`data-type`属性](https://guides.rubyonrails.org/working_with_javascript_in_rails.html#data-type)改变 AJAX 调用的预期数据类型**。支持的数据类型有`"text"`(明文响应)、`"html"`、`"xml"`、`"json"`和默认`"script"`(详见[来源](https://github.com/rails/rails/blob/master/actionview/app/assets/javascripts/rails-ujs/utils/ajax.coffee#L7))。

#### 不要再指望`trigger()` UJS 的行动了

假设您有一个与 UJS 远程处理的链接(通过`remote: true`属性),并且您想以编程方式“点击”该链接。以前，您可以简单地调用`$('a#my-link').trigger('click')`，这将触发 AJAX 调用。在`rails-ujs`，这些都不再管用了。

简单解释就是 jQuery [`trigger()`函数](https://api.jquery.com/trigger/)只能触发 jQuery 中再次处理的事件，它们*是`jquery-ujs`中的*。由于`rails-ujs`完全没有 jQuery，所以您必须**将这样的代码转换成本地事件分派**。在我们的简单示例中，您可以只调用本机的 [`click()`方法](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/click)来触发 AJAX，其他时候您可能想要查看 [`dispatchEvent()`方法](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/dispatchEvent)。

### 期末笔记

总的来说，在我们的代码库中经历了所有这些之后，迁移感觉也像是一个*修复*的任务，因为它迫使我们完全理解并纠正过去我们不够关心的代码片段。仅此一点就证明了我们迁移工作的合理性！

我希望这里描述的小问题不会阻止您将代码升级到`rails-ujs`。他们不应该，在你读完这些之后！；-)谢谢！