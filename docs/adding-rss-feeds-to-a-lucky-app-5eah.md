# 向幸运应用添加 RSS 源

> 原文：<https://dev.to/mitchartemis/adding-rss-feeds-to-a-lucky-app-5eah>

这只是一个给任何想在 Crystal Lucky Framework 中实现 RSS 提要的人的快速帖子。这篇文章适用于 Lucky 0.14.1，但也应该适用于 0.15。

感谢 [@paulcsmith](https://twitter.com/paulcsmith) 和[@ jeremywortink](https://twitter.com/jeremywoertink)在 Gitter 帮我解决这个问题。

如果你还没听说过 Lucky，可以查看网站[这里](https://luckyframework.org/)。这是一个用[水晶](https://crystal-lang.org/)写的网络框架

首先，创建一个继承自`Lucky::Action`的`Action`。我们将添加一个名为`xml`的方法，可以在您的每个操作中调用它，为提要传递数据。然后，`xml`方法将使用 Crystal 内置的 xml Builder 创建 XML 字符串，并迭代您传入的数据。

```
# src/actions/xml_action.cr
require "xml"
abstract class XMLAction < Lucky::Action
    def title
        "Website RSS Feed"
    end

    def description
        "Updates for Website"
    end

    def link
        "https://websiteurl.dev"
    end

    private def xml(articles : ArticleQuery)
        string = XML.build(indent: "  ", encoding: "UTF-8") do |xml|
            xml.element(
                "rss", 
                version: "2.0", 
                "xmlns:dc": "http://purl.org/dc/elements/1.1/",
                "xmlns:content": "http://purl.org/rss/1.0/modules/content/",
                "xmlns:atom": "http://www.w3.org/2005/Atom",
                "xmlns:media": "http://search.yahoo.com/mrss/"
                ) do
                xml.element("channel") do
                    xml.element("title") { xml.cdata title }
                    xml.element("description") { xml.cdata description }
                    xml.element("link") { xml.text link }
                    xml.element("generator") { xml.text "Lucky Framework" }
                    xml.element("lastBuildDate") { xml.text Time.utc_now.to_s }
                    xml.element("atom:link") { 
                        xml.attribute "href", "#{link}#{request.path}"
                        xml.attribute "rel", "self"
                        xml.attribute "type", "application/rss+xml"
                    }
                    xml.element("ttl") { xml.text "60" }
                    articles.each do |article|
                        xml.element("item") do
                            # title, description, link, category, dc:creator, pubDate, content:encoded
                            xml.element("title") { xml.cdata article.title }
                            if article.meta_description
                                xml.element("description") { xml.cdata article.meta_description.not_nil! }
                            end
                            xml.element("link") { xml.text "#{link}articles/#{article.slug}" }             xml.element("dc:creator") { xml.cdata "Author Name" }
                            xml.element("pubDate") { xml.text article.created_at.to_s }
                            if article.og_image
                                xml.element("media:content") do
                                    xml.attribute "url", "article.og_image"
                                    xml.attribute "medium", "image"
                                end
                            end
                            if article.content
                                content = Markdown.to_html(article.content.not_nil!)
                                xml.element("content:encoded") { xml.cdata content }
                            end
                        end
                    end

                end
            end
        end
        Lucky::TextResponse.new(context, content_type: "text/xml; charset=utf-8", body: string status: 200)
    end
end 
```

尽管这个抽象类看起来很大，但除了生成输出到浏览器的 XML 之外，它实际上没做什么。这样做将大大简化我们的动作类。

在这个例子中，我们传入一个`ArticleQuery`，你需要在你自己的应用中实现它。作为参考，我的有一个`published`的范围，所以我可以阻止未发表的文章被查看。

```
class ArticleQuery < Article::BaseQuery
  def published
    published_at.lte(Time.now)
  end
end 
```

`Article`模型有以下属性:`title`、`meta_description`(可选)、`slug`、`content`(可选)、`og_image`(可选)。出于您自己的目的，您也需要更新它。

在`XMLAction#xml`方法的最后，我们使用`Lucky::TextResponse`将 XML 字符串发送到浏览器。请注意，`application/rss+xml`是适当的响应内容类型，但是，如果您希望它在 web 浏览器中可见，您需要使用`text/xml; charset=utf-8`(至少 Ghost 是这样做的)。

## 示例动作

因为所有的 XML 逻辑都在`XMLAction`中，所以我们可以让我们的操作变得非常干净。在下面的例子中，`Rss::Index`覆盖了提要标题，然后通过`xml`方法传入一个已发表文章的列表。

```
# src/actions/rss/index.cr
class Rss::Index < ::XMLAction

    def title
        "Latest Dailies"
    end

    get "/feeds/all.rss" do
        articles = ArticleQuery.new.published.published_at.desc_order
        xml articles
    end

end 
```