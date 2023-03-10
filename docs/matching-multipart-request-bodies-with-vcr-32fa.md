# 将多部分请求正文与 VCR 匹配

> 原文：<https://dev.to/kabisasoftware/matching-multipart-request-bodies-with-vcr-32fa>

当使用 VCR 测试 HTTP 多部分请求时，由于多部分的边界，您可能会遇到不匹配的盒子。多部分边界通常是由 HTTP 库或浏览器随机生成的，因此当 VCR 试图将多部分请求与磁带匹配时，边界将会不同，匹配将会失败。

然而，在匹配请求之前，用固定边界替换随机边界是非常简单的。多部分请求将有一个`Content-Type`头，它不仅使请求可被识别为多部分请求，还指定了所使用的确切边界。在这种情况下，我从 Ruby 项目中提取了一个磁带，所以`Content-Type`看起来像这样:

```
multipart/form-data; boundary=----RubyFormBoundaryTsqIBL0iujC5POpr 
```

给定这个头，我们应该能够简单地用一个固定值替换边界。VCR 有一个名为`match_request_on`的配置选项，它是一个数组，接受预定义请求匹配器的符号(参见 https://relishapp.com/vcr/vcr/v/5-0-0/docs/request-matching)或 callable (Proc，Lambda)。我不是一个真正喜欢使用进程的人，但是只要我们创建一个响应`call`的对象，我们就会没事:

```
class VCRMultipartBodyMatcher
  MULTIPART_HEADER_MATCHER = %r{^multipart/form-data; boundary=(.+)$}

  def call(request_1, request_2)
    normalized_multipart_body(request_1) == normalized_multipart_body(request_2)
  end

  private

  def normalized_multipart_body(request)
    content_type = (request.headers['Content-Type'] || []).first.to_s

    return request.body unless multipart_request?(content_type)

    boundary = MULTIPART_HEADER_MATCHER.match(content_type)[1]
    request.body.gsub(boundary, '----RubyFormBoundaryTsqIBL0iujC5POpr')
  end

  def multipart_request?(content_type)
    return false if content_type.empty?

    MULTIPART_HEADER_MATCHER.match?(content_type)
  end
end 
```

现在我们可以告诉 VCR 使用我们的自定义匹配器:

```
VCR.configure do |config|
  config.default_cassette_options = {
    match_requests_on: [:method, :uri, VCRMultipartBodyMatcher.new]
  }
end 
```

现在，当运行测试时，请求和片盒的边界将是相等的，因此只有当请求参数不同时，片盒才会不匹配。👍

您需要测试应用程序的帮助吗？在 Kabisa，我们非常了解如何编写好的测试。如果您想联系我们，请给我们留言。