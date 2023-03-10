# 重构条件以获得更具可读性的代码

> 原文：<https://dev.to/brianmcoates/refactoring-conditionals-anl>

今天我正在写一些代码，当我开始写这个长条件的时候感觉很奇怪。我问我的一个同事，他是否有任何想法，我们可以做些什么，使这更具可读性。我们做了以下工作

在
之前

```
 class GetStuff

    def fetch(current_url)
      response = handle_response(query).try(:first)
      return nil if !enabled?(response) && !postable?(response) && current_url == response.global_banner.first.button_url

      response
    end

    private

    def enabled?(value)
      return false if value.nil?
      value.enabled
    end

    def postable?(value)
      Time.at(value.post_date) < Time.now && value.enabled
    end
  end 
```

在
之后

```
 class GetStuff

    def fetch(current_url)
      @current_url = current_url
      @response = handle_response(query).try(:first)

      return nil unless show?

      @response
    end

    private

    def show?
      return false if @response.nil?
      enabled? && postable? && is_current_page?
    end

    def is_current_page?
      false if @current_url == @response.global_banner.first.button_url
    end

    def enabled?
      @response.enabled
    end

    def postable?
      Time.at(@response.post_date) < Time.now && @response.enabled
    end
  end 
```

我们在这里做了几件事，去掉了一些反向逻辑。

```
!enabled?(response) && !postable?(response) && current_url == response.global_banner.first.button_url 
```

我们不需要！再也不会了。我不知道你怎么想，但是我越少考虑这件事是不是真的，就越好。

我们还通过创建一些实例变量来简化传递给方法的变量。

在
之前

```
def fetch(current_url)
 response = handle_response(query).try(:first)
 return nil if !enabled?(response) && !postable?(response) && current_url == 
  response.global_banner.first.button_url

 response
end 
```

在
之后

```
def fetch(current_url)
 @current_url = current_url
 @response = handle_response(query).try(:first)

 return nil unless show?

 @response
end 
```

我们有一个保护我们免受服务器空响应的 gaurd 子句。我们已经有了这个，但不太清楚它在做什么，它在保护一切。

在
之前

```
return nil if !enabled?(response) && !postable?(response) && current_url == response.global_banner.first.button_url

def enabled?(value)
  return false if value.nil?
  value.enabled
end 
```

在
之后

```
def show?
  return false if @response.nil?
  enabled? && postable? && is_current_page?
end 
```

事情更具可读性，这些方法解释了为什么我们有适当的检查，以及这种情况在做什么。我觉得这是自我记录的代码。