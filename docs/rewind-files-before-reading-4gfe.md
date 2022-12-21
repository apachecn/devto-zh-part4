# 阅读前倒回文件

> 原文：<https://dev.to/spitsman/rewind-files-before-reading-4gfe>

假设您有一个带有文件输入的表单，您需要通过您的 Rails 应用程序将该文件转发给外部服务。Params 将如下所示:

```
{ "file"=> #<ActionDispatch::Http::UploadedFile:0x00007fce9a8af140 @tempfile=#<Tempfile:/path/to/the/file.xlsx>, ... > } 
```

有时第一次无法到达外部服务，因此您决定设置一些重试次数。

```
retry_count = 5
while retry_count > 0
    begin
        HTTPClient.new.post(url, params, headers)
    rescue ExternalServiceUnavailable => e
        retry_count -= 1
    end
end 
```

上面的代码使用 HTTPClient gem 发出 http 请求，如果至少有一次重试，它将引发以下错误:
`ArgumentError: Illegal size value: #size returns 154139 but cannot read`
事情是这样的:`HTTPClient`使用`IO#read`逐块读取文件以发出 POST 请求。`lineno`指针在`read`之后到达文件的末尾，你不能再从 I/O 流中读取字节:

```
params['file'].tempfile.eof? # => true 
```

要解决这个问题，使用`IO#rewind`–它将`IO`定位到输入的开始，将`lineno`重置为零。只需在每个请求前添加检查文件`eof`并在需要时添加`rewind`:

```
retry_count = 5
while retry_count > 0
    begin
        params['file'].tempfile.rewind if params['file'].tempfile.eof? # check eof
        HTTPClient.new.post(url, params, headers)
    rescue ExternalServiceUnavailable => e
        retry_count -= 1
    end
end 
```