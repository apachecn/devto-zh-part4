# 第 4 集-类型

> 原文：<https://dev.to/victoravelar/episode-4-types-2l9i>

📷照片由[阿玛多·洛雷罗](https://unsplash.com/photos/BVyNlchWqzs)在 Unsplash 上拍摄

嘿，我们越来越接近能够使用开发到 API。

### 什么是类型

根据这篇文章，我发现这个定义更容易理解

> 类型系统是编程语言最重要的特性，它允许你组织你的应用程序数据。Go 的类型系统遵循极简主义的方法。它提供了几个内置类型，如 string、bool、int 和 float64。

你可以想象一种类型，比如这里描述的原语，它描述了内存中需要的空间，以及将要分配到那里的数据类型。

例如对于类型用户:

```
// User contains information about a devto account
type User struct {
    Name            string  `json:"name,omitempty"`
    Username        string  `json:"username,omitempty"`
    TwitterUsername string  `json:"twitter_username,omitempty"`
    GithubUsername  string  `json:"github_username,omitempty"`
    WebsiteURL      *WebURL `json:"website_url,omitempty"`
    ProfileImage    *WebURL `json:"profile_image,omitempty"`
    ProfileImage90  *WebURL `json:"profile_image_90,omitempty"`
} 
```

Enter fullscreen mode Exit fullscreen mode

我们告诉 go 编译器，每次某个东西被分配了`devto.User`类型时，它应该在内存中分配足够的空间来放置一个具有这种结构的对象。

这里有一个惊人的存储库，里面有许多 [go 示例](https://github.com/golang/example/tree/master/gotypes#types)

### WTF 是那些东西之后的类型？

这些被称为标签，对我来说，这是做注释的一种方式，来自 PHP，至少看起来是这样。

结构类型定义中的 Go 规范将标记定义为:

> 字段声明后面可以跟一个可选的字符串文字标记，它成为相应字段声明中所有字段的属性。空标签字符串相当于缺少标签。标记通过反射接口可见，并参与结构的类型标识，但在其他情况下被忽略。

这将帮助我们做一些叫做 JSON 解组的事情，但是不要着急，我们还没有完成！

### 实际开发类型

为了从 API 文档中获取分析示例响应所需的类型，我们将使用 [Get article spec](https://docs.dev.to/api/#tag/articles/paths/~1articles~1%7Bid%7D/get) ,因为对于创建请求，我们只有应该提交的参数，在 Get article 端点中，您可以获取文章信息以及 devto 在后台添加的所有元数据。

现在，我建议你不要在创建类型时嵌套结构，因为它会很快变得混乱，相反，尝试将每个嵌套的对象分解成它自己的类型。

因此，您可以检查我在项目存储库中找到的类型，我还为 URL 添加了一些自定义的解组。

这里我将只留下`devto.Article`类型，这是主要的 dev.to 类型。

```
// Article contains all the information related to a single
// information resource from devto.
type Article struct {
    TypeOf                 string       `json:"type_of,omitempty"`
    ID                     uint32       `json:"id,omitempty"`
    Title                  string       `json:"title,omitempty"`
    Description            string       `json:"description,omitempty"`
    CoverImage             *WebURL      `json:"cover_image,omitempty"`
    SocialImage            *WebURL      `json:"social_image,omitempty"`
    PublishedAt            *time.Time   `json:"published_at,omitempty"`
    EditedAt               *time.Time   `json:"edited_at,omitempty"`
    CrossPostedAt          *time.Time   `json:"crossposted_at,omitempty"`
    LastCommentAt          *time.Time   `json:"last_comment_at,omitempty"`
    TagList                Tags         `json:"tag_list,omitempty"`
    Tags                   string       `json:"tags,omitempty"`
    Slug                   string       `json:"slug,omitempty"`
    Path                   *WebURL      `json:"path,omitempty"`
    URL                    *WebURL      `json:"url,omitempty"`
    CanonicalURL           *WebURL      `json:"canonical_url,omitempty"`
    CommentsCount          uint         `json:"comments_count,omitempty"`
    PositiveReactionsCount uint         `json:"positive_reactions_count,omitempty"`
    PublishedTimestamp     *time.Time   `json:"published_timestamp,omitempty"`
    User                   User         `json:"user,omitempty"`
    Organization           Organization `json:"organization,omitempty"`
    BodyHTML               string       `json:"body_html,omitempty"`
    BodyMarkdown           string       `json:"body_markdown,omitempty"`
    Published              bool         `json:"published,omitempty"`
} 
```

Enter fullscreen mode Exit fullscreen mode

看一看，提出你的问题，如果你觉得有什么不对劲，就贡献出来。

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)/[devto-API-go](https://github.com/VictorAvelar/devto-api-go)

### 开发到 API 的 go 客户端

<article class="markdown-body entry-content container-lg" itemprop="text">

# 开发应用编程接口

开发到 API 的 go 客户端

[![License: MIT](img/db85796de36d003cae919db6f133f455.png)](https://opensource.org/licenses/MIT)

#### 特拉维斯·CI

[![Build Status](img/2f1b58bd484d692482b6dfbc5f4cf54d.png)](https://travis-ci.org/VictorAvelar/devto-api-go)

#### 仔细或彻底检查

[![Scrutinizer Build Status](img/dd26fac85af7836c178831bb4174749a.png)](https://scrutinizer-ci.com/g/VictorAvelar/devto-api-go/build-status/master)[![Scrutinizer Code Quality](img/4e95ffe53e71af50f0cf446342069fdf.png)](https://scrutinizer-ci.com/g/VictorAvelar/devto-api-go/?branch=master)[![Code Coverage](img/4b24d605b035f3712c4061b10d4c59ed.png)](https://scrutinizer-ci.com/g/VictorAvelar/devto-api-go/?branch=master)

#### Go 生态系统

[![Go Report Card](img/9b9b43938b0abe1ad6b7bf883b9ce923.png) ](https://goreportcard.com/report/github.com/VictorAvelar/devto-api-go) [ ![GoDoc](img/8074e2e7c6159296ccb342c7f3aea036.png)](https://godoc.org/github.com/VictorAvelar/devto-api-go)

## 路标

*   基本客户端和配置
*   文章资源
*   CLI 实用程序
*   认证资源
*   扩展测试套件
*   提供代码示例

## 放弃

这个库不是一个官方的开发者 API 库，而是对开发者社区的友好贡献

</article>

[View on GitHub](https://github.com/VictorAvelar/devto-api-go)