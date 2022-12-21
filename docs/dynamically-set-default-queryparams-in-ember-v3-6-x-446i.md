# 在 ember > v3.6.x 中动态设置默认的 queryParams

> 原文：<https://dev.to/michalbryxi/dynamically-set-default-queryparams-in-ember-v3-6-x-446i>

假设你有一个应用程序，路径如下:

```
https://example.com/calendar 
```

Enter fullscreen mode Exit fullscreen mode

该路径可以接受可选的*查询参数* `from`和`to`，这些参数将决定*日历*的哪一部分应该显示:

```
https://example.com/calendar?from=2019-09-01&to=2019-09-08 
```

Enter fullscreen mode Exit fullscreen mode

现在假设有一个要求，让用户访问*裸 URL* 并显示*当前周*。

一种选择是检查`from`和`to`是否被置位，如果没有，则在代码逻辑内的**中填入各自的值。**

我坚信应该从*的 URL* 中尽可能多地获取应用程序的*状态。所以我建议:检查是否设置了`from`和`to`，如果没有，将应用重定向到 *URL* ，并将那些 *queryParams* 设置为各自的值。或者换句话说:从外部的**导出状态。***

我认为这是个好主意的主要原因是:

*   该网址可以加入书签和共享，每个用户都将看到相同的数据。
*   我们必须在**的一个地方**设置*查询参数*，应用程序的每个部分都可以从那个地方读取值。

我在我的应用程序中实现了重定向方法:

```
// COMPATIBLE WITH ember v3.4.3

import Route from "@ember/routing/route";
import moment from "moment";

export default Route.extend({
  queryParams: {
    from: { refreshModel: true },
    to: { refreshModel: true },
  },

  beforeModel(transition) {
    let params = transition.queryParams;
    if (!params.from || !params.to) {
      this.replaceWith({
        queryParams: {
          from: moment()
            .startOf("week")
            .format(dateFormat),
          to: moment()
            .endOf("week")
            .format(dateFormat)
        }
      });
    }
  },
}); 
```

Enter fullscreen mode Exit fullscreen mode

这在我升级到 **ember v3.12.0** 时停止了工作。通过挖掘变更日志和[拉请求](https://github.com/emberjs/ember.js/commit/d4d86e178e1df64dc9f4d14d0112bf7a5e3e4dfc)我发现在 **ember v3.6.x** 中新增了`RouteInfo`对象`from`和`to`T9。

所以我做了相应的修改，现在代码看起来像:

```
// COMPATIBLE WITH ember > v3.6.x

import Route from "@ember/routing/route";
import moment from "moment";

export default Route.extend({
  queryParams: {
    from: { refreshModel: true },
    to: { refreshModel: true },
  },

  beforeModel(transition) {
    let params = transition.to.queryParams; // << This line has changed
    if (!params.from || !params.to) {
      this.replaceWith({
        queryParams: {
          from: moment()
            .startOf("week")
            .format(dateFormat),
          to: moment()
            .endOf("week")
            .format(dateFormat)
        }
      });
    }
  },
}); 
```

Enter fullscreen mode Exit fullscreen mode

从 PR 中的措辞我得出一个结论，我正在使用一个私有的`transition`对象的 API，这从来都不是一个好主意。

* * *

在 [unsplash](https://unsplash.com) 上的 [Diomari Madulara](https://unsplash.com/@diomari) 的封面照片。