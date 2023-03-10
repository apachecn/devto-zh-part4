# 如何设置只读 Rails 控制台

> 原文：<https://dev.to/molly/how-to-setup-a-readonly-rails-console-1j1a>

为了更好地保护我们的生产数据，去年我们选择设置一个只读生产控制台。这使得开发人员可以随意查看生产数据，而不必担心他们可能会意外地更改一些他们不应该更改的内容。在这篇文章中，我将详细说明我们为了完成 Ruby on Rails 应用程序所做的工作。

在深入每个数据库的细节之前，我想首先提到我们使用完全独立的服务器进行控制台访问。使用单独的服务器允许我们调整应用程序设置，以实现只读访问。为了部署这些变更，我们使用了 [Ansible](https://www.ansible.com/) 。当 Ansible 运行 deploy 时，它会查找控制台框标记，以了解需要将哪些设置和配置部署到那个特定的框中。

# [MySQL](#mysql)

我们做的第一件事是在 MySQL 中设置一个只读访问的用户。然后，为了使我们的应用程序对 MySQL 是只读的，我们只需将只读用户凭证放在控制台服务器上的`database.yml`文件中。

```
production:
  adapter: mysql2
  encoding: utf8
  reconnect: true
  pool: 16
  database: "prod_db"
  username: "readonly"
  password: "you_wish"
  host: "127.0.0.1"
  strict: false 
```

Enter fullscreen mode Exit fullscreen mode

现在，任何时候有人在我们的控制台服务器上打开 Rails 控制台，它都会自动使用配置文件中的只读凭证。

然而，有时我们仍然希望允许开发者通过控制台编辑数据。为了实现这一点，我们设置了一个 bash 脚本，用于打开 Rails 控制台。在这个 bash 脚本中，我们选择使用 Rails 提供的一个鲜为人知的特性，`DATABASE_URL`。如果在您的环境中设置了 [`DATABASE_URL`](https://edgeguides.rubyonrails.org/configuring.html#configuring-a-database) 变量，Rails 将使用它连接到您的数据库，而不是从您的`database.yml`文件中读取。这允许我们在需要时覆盖我们的数据库配置。我们在 bash 脚本中这样设置它:

```
#!/bin/bash
cd /application_path
if [ "$1" = 'write' ]; then export DATABASE_URL="mysql2://write_username:write_password@host/db_name"
fi RAILS_ENV=production /usr/local/bin/bundler exec rails console 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果开发人员需要编辑数据，他们可以使用命令`console write`打开一个写控制台。

# 再说一遍

为了将 Redis 设置为 readonly，我们选择覆盖 Redis 客户端，以显式地阻止任何写命令。因为我们有一个 Ruby on Rails 应用程序，所以我们使用 [redis-rb](https://github.com/redis/redis-rb) gem 来与 redis 对话。为了阻止写命令，我们首先通过调用 Redis 客户机上的`command`方法收集了所有基于写的命令。作为参考，`Rails.cache.data`简单给你你的 Redis 客户端。

```
dev> Rails.cache.data
=> #<Redis client v4.0.3 for redis://127.0.0.1:6379/15> 
```

Enter fullscreen mode Exit fullscreen mode

`command`方法将返回 Redis 实例将响应的所有命令的数组，以及关于每个命令的一些附加信息。

```
dev> Rails.cache.data.command.first(3)
=> [["expireat", 3, ["write", "fast"], 1, 1, 1],
 ["setnx", 3, ["write", "denyoom", "fast"], 1, 1, 1],
 ["getrange", 4, ["readonly"], 1, 1, 1]] 
```

Enter fullscreen mode Exit fullscreen mode

为了只过滤出写命令，我们简单地检查命令属性列表中的“write”值。

```
WRITE_COMMANDS = Rails.cache.data.command.map { |a| a[0] if a[2].include?('write') }.compact.to_set 
```

Enter fullscreen mode Exit fullscreen mode

一旦我们有了一个写命令列表，我们就在我们的 gem 中覆盖了 [`process`方法](https://github.com/redis/redis-rb/blob/d21527d218079aeb91b7d6488a39c17295bfba1d/lib/redis/client.rb#L229),以便在调用这些方法中的任何一个时抛出一个错误。

```
def process(commands)
  if commands.flatten.any? { |c| WRITE_COMMANDS.include?(c.to_s) }
    raise NotImplementedError, "REDIS_ACCESS_MODE is set to 'readonly', disallowing writes"
  end

  # additional method logic
end 
```

Enter fullscreen mode Exit fullscreen mode

这两部分允许我们阻止 Redis 写命令。但是，问题仍然存在，我们如何只在我们的控制台上阻止这些写命令？我们再次转向环境变量和 bash 控制台脚本。在我们的控制台脚本中，我们根据控制台是默认的只读控制台还是写控制台来设置环境变量。

```
#!/bin/bash
cd /application_path
if [ "$1" = 'write' ]; then
  export DATABASE_URL="mysql2://write_username:write_password@host/db_name"
  export REDIS_ACCESS_MODE=""
else
  export REDIS_ACCESS_MODE="readonly"
fi
RAILS_ENV=production /usr/local/bin/bundler exec rails console 
```

Enter fullscreen mode Exit fullscreen mode

然后，在我们的应用程序的`redis.rb`初始化文件中，如果在只读访问模式下调用了写命令，我们就为 process 方法打了一个猴子补丁以返回一个错误。

```
if ENV['REDIS_ACCESS_MODE'] == 'readonly'
  class Redis
    class Client
      WRITE_COMMANDS = ::Rails.cache.data.command.map { |a| a[0] if a[2].include?('write') }.compact.to_set.freeze

      def process(commands)
        if commands.flatten.any? { |c| WRITE_COMMANDS.include?(c.to_s) }
          raise NotImplementedError, "REDIS_ACCESS_MODE is set to 'readonly', disallowing writes"
        end

        # additional method logic 
      end
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

嘣！默认情况下，控制台框对于 MySQL 和 Redis 是只读的。只剩下一块拼图了，弹性搜索。

# 弹性搜索

Elasticsearch 是我们应用程序的基石，所以我们也需要它是只读的。为了与 Elasticsearch 对话，我们使用了 [elasticsearch-ruby](https://github.com/elastic/elasticsearch-ruby) 宝石。就像我们在 Redis 中做的一样，我们找到了用于向 Elasticsearch 发出外部请求的核心方法， [`perform_resquest`](https://github.com/elastic/elasticsearch-ruby/blob/master/elasticsearch-transport/lib/elasticsearch/transport/client.rb#L160) ，并对其打了补丁，这样每当执行一个写方法时，它都会引发一个错误。因为我们使用 HTTP 请求与 Elasticsearch 对话，所以我们想要阻止的方法是 PUT、POST 和 DELETE。

```
module Elasticsearch
  module Transport
    class Client
      if ENV['ELASTICSEARCH_ACCESS_MODE'] == 'readonly'
        def perform_request(method, path, params={}, body=nil, headers=nil)
          raise 'Elasticsearch is in readonly mode.' if method.to_s.match?(/PUT|POST|DELETE/)
          method = @send_get_body_as if 'GET' == method && body
          transport.perform_request(method, path, params, body, headers)
        end
      end
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

同样，我们也选择使用一个环境变量来决定我们是否应该修补`perform_request`方法。然后，我们将该环境变量添加到 bash 脚本中。我们完成的 bash 脚本如下所示:

```
#!/bin/bash
cd /application_path
if [ "$1" = 'write' ]; then
  export DATABASE_URL="mysql2://write_username:write_password@host/db_name"
  export REDIS_ACCESS_MODE=""
  export ELASTICSEARCH_ACCESS_MODE=""
else
  export REDIS_ACCESS_MODE="readonly"
  export ELASTICSEARCH_ACCESS_MODE="readonly"
fi
RAILS_ENV=production /usr/local/bin/bundler exec rails console 
```

Enter fullscreen mode Exit fullscreen mode

这个脚本确保当开发人员或支持人员使用`console`命令打开控制台时，默认情况下它是只读的。必要时，如果他们需要更新任何数据，可以调用`console write`。尽管打开一个写控制台非常容易，但是大多数时候人们都在只读控制台中工作。只读控制台已经多次证明了自己，它可以避免人们在浏览生产数据时犯愚蠢的错误。

# 其他选项

在处理生产数据时，还有许多其他方法来实现数据安全。这只是一种方法，也是我们在肯纳选择使用的方法。另一个非常受欢迎的选择是制作数据的副本或克隆，然后允许人们对该副本或克隆运行他们想要的任何查询。这样做的缺点是，根据数据集的大小，每次需要时获取最新的复本都会非常耗时。此外，克隆单个数据库(如 MySQL)非常简单。然而，当您使用 3 个不同的数据存储库时，就像我们在 Kenna 所做的那样，将所有的数据复制到一起需要付出更多的努力。

在 Kenna，开发人员有能力克隆生产数据，但目前仅在 MySQL 中可用。通常，MySQL 克隆仅用于检查高风险的迁移或脚本，这些迁移或脚本将一次更改大量生产数据。除此之外，我们发现我们的 readonly 控制台对我们的用例非常有用，因为大多数时候开发人员和支持人员只想查看最新的生产数据。

希望这篇文章对你有用！一如既往，如果您有任何问题，请告诉我！🤗