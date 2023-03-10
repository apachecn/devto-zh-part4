# 提前键入异步请求限制

> 原文：<https://dev.to/jetrockets/typeahead-async-requests-throttling-39dk>

因为每个人都有一些遗留代码(谁没有呢？)，而昨天我们面临的问题。

我们的内部应用程序有搜索面板，用户可以写并获得相关的结果。但是搜索是用 typeahead 编写的，没有任何限制。因此，每次输入更改都会触发后端的全文搜索，这会导致数据库连接池耗尽。

我们没有找到任何现成的解决方案，所以我们添加了以下修复:

```
$input.typeahead(
  {
    ...
  },
  {
        ...
    async: true
    source: (query, syncResults, asyncResults) ->
      href = self.href
      # Added getAsyncResults call to throttle requests:
      getAsyncResults(
        () ->
          $.ajax(
            url: href
            data:
              query: query
            dataType: 'json'
          ).success(
            (data, textStatus, jqXHR) ->
              asyncResults(data)
          )
      )
  }) 
```

其中 getAsyncResults 在 typeahead init 之前定义:

```
# throttle is lodash function
getAsyncResults = _.throttle(((fn) ->
    fn()
  ), 300,
  { ... }) 
```

如果有人觉得这个简单的解决方案有用，我会很高兴。