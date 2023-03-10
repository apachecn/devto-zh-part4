# 正确报告 graphql-ruby 中不可为空的违规

> 原文：<https://dev.to/evilmartians/reporting-non-nullable-violations-in-graphql-ruby-properly-2k3b>

GraphQL 鼓励你将前端和后端分离。您可以多考虑类型的“正确性”，少考虑特定的视图布局。为了实现这一点，GraphQL 提供了强类型系统。GraphQL 不允许返回字符串而不是整数，也不允许返回 API 声明类型为*不可空*的`null` s。

但是有时您的数据会意外地包含空值，甚至在您不期望的地方。一些外部 API 将返回不正确或丢失的数据，即使它的*契约*不允许这样做。有时候会是自己的 bug。但是在任何情况下，您都有责任跟踪此类故障并纠正它们。

如果您使用 Ruby 开发 GraphQL API，那么您使用 graphql-ruby 的可能性很高。它是 Ruby 中事实上的标准，因为它是一个非常成熟、功能丰富的 gem，有一个完整的插件生态系统。但是“开箱即用”,它不会通知您违反了这些“不可空类型”规则，而只是悄悄地向客户端返回一个带有错误消息的部分响应。

但是我们想知道非空违规，因为开发防弹应用程序是我们的责任。根据 graphql-ruby 关于类型错误的[文档，有一个特殊的钩子来捕捉这种错误。让我们使用它！](https://graphql-ruby.org/errors/type_errors)

我们使用的是蜜獾，所以我们将在那里报告这种攻击:

```
class YourAppSchema < GraphQL::Schema
  def self.type_error(exception, query_context)
    if exception.is_a?(::GraphQL::InvalidNullError)
      Honeybadger.notify exception, context: { query: query_context.query.query_string }
    end
    super
  end
end 
```

到目前为止，一切顺利。很快我们将能够在错误跟踪界面中看到我们的第一个错误。

但是等等，这是什么？在一个错误字段中混合的多个不同字段中存在关于非空违规的错误。他们不是不同的虫子吗？怎么了?

原来默认情况下 [Honeybadger 按照异常类、组件(控制器和动作)和回溯片段将错误分组到一起](https://docs.honeybadger.io/lib/ruby/getting-started/customizing-error-grouping.html)。请注意，异常消息不算。在 GraphQL 的情况下，它总是一个单独的控制器和动作，所以完全不同的查询的异常被组合在一起。让我们解决这个问题！

根据[相同的文档页](https://docs.honeybadger.io/lib/ruby/getting-started/customizing-error-grouping.html)，我们需要为每个不同的错误计算唯一的*指纹*字符串。那么，对于非空违例，我们需要在这个指纹中包含什么呢？

*   例外类？是的，我们需要将`GraphQL::InvalidNullError`与其他类型的错误区分开来
*   控制器和动作？不，他们总是一样的。即使在触发订阅的过程中会出现错误，会有什么不同吗？
*   回溯？嗯…我不确定。你怎么想呢?
*   当然是类型和字段名！我们需要区分不同领域的错误。

让我们使用异常类名、GraphQL 类型名和字段名:

```
class ApplicationSchema < GraphQL::Schema
  class << self
    def type_error(exception, query_context)
      fingerprint = honeybadger_fingerprint(exception)
      Honeybadger.notify exception, fingerprint: fingerprint, context: {
        query: query_context.query.query_string,
        user_id: query_context[:user]&.id,
      }
      super
    end

    private

    def honeybadger_fingerprint(error)
      case error
      when GraphQL::InvalidNullError
        "#{error.class}:#{error.parent_type.name}.#{error.field.name}"
      end
    end
  end
end 
```

就是这样:从现在开始，不同领域的异常存在于不同的蜜獾错误中。

感谢您的关注！让我们构建可靠的 API 吧！