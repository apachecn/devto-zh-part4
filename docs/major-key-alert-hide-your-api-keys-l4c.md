# 主要键警报:隐藏您的 API 键

> 原文：<https://dev.to/kcarrel/major-key-alert-hide-your-api-keys-l4c>

我喜欢将公共 API(应用程序编程接口)集成到我的项目中，以便添加更多的功能。然而，这带来了处理隐藏 API 键的问题！这篇文章将带你了解 API 密匙的用途，为什么你需要隐藏它们，并提供一个如何在 Ruby 项目中实现这一点的指南。

# 什么是 API key？

API 密钥是用于控制对 API 服务提供者的访问的唯一标识符。API 密钥链接到被分配了密钥的用户，并且可以跟踪用户对 API 的调用量或者用户被允许访问 API 的哪些区域。

# 为什么我需要隐藏我的 API 密钥？

隐藏您的 API 键是必要的，因为它是您访问资源的标识符。如果您公开该标识符，其他人可以使用它来代表自己，滥用您对资源的访问，这可能会导致您的访问被关闭。通过将这些密钥隐藏在一个已被。gitignore(d ),这样当你把你的项目放入 github 库时，这些 API 键就不是公开的，也不会容易受到攻击。

# 隐藏那些钥匙！(鲁比)

*   将 gem 'dotenv-rails '添加到您的 gem 文件并捆绑安装

*   在项目目录的根目录中，使用命令' $ touch'。'环境'

*   在那。env 文件创建一个变量(仅大写)并将其分配给你的 API 键

```
#Inside the .env file

YELP_API_KEY=your_developer_api_key_from_yelp 
```

*   添加您的。env 文件添加到。gitignore 文件

```
#Inside the .gitignore file

.env 
```

*   您可以在整个 ruby 项目中访问 API 键，如下例所示

```
def yelp
   yelp = ENV["YELP_API_KEY"]
   yelp_url = "https://cors-anywhere.herokuapp.com/https://api.yelp.com/v3/businesses/search?term=#{category}&location=#{location}"
   res = HTTParty.get(yelp_url, :headers => {"Authorization" => "Bearer #{yelp}", "x-requested-with" => "XMLHttpRequest"})
    render plain: res.body.squish
end 
```

快乐的 API 密钥隐藏！

# 常见故障排除

*   找不到您新创建的。之后的 env 文件。无视它？检查文本编辑器上的设置，看看它是否使隐藏文件在项目树中可见。
*   你已经把你的 API 密匙上传到 github 了吗？看看这个:[https://help . github . com/en/articles/remove-sensitive-data-from-a-repository](https://help.github.com/en/articles/removing-sensitive-data-from-a-repository)