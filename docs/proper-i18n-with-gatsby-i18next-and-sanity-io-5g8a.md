# 我和盖茨比，下一个和理智在一起

> 原文：<https://dev.to/jospohr/proper-i18n-with-gatsby-i18next-and-sanity-io-5g8a>

> 先决条件:
> 盖茨比和理智的基本知识. io
> 
> TL；你可以在这里找到启动源[，在这里](https://github.com/johannesspohr/gatsby-starter-i18next-sanity)找到演示

Gatsby 是一个非常棒的工具，可以用来构建速度极快、静态生成的网站。然而，为构建多语言网站提供的解决方案非常少，尤其是当性能、SEO 和用户体验方面的要求很高时。

在我的网站项目中，我需要:

*   语言的正确 URL(路径中的语言，翻译的 slugs)
*   来自 sanity 和其他来源的多语言内容
*   片段的正确翻译
*   优化的包大小(不要一次发送所有翻译)
*   到其他语言的备用链接
*   带有语言信息的网站地图

在查看其他解决方案时，我没有发现 gatsby-plugin-i18n 能够满足那些要求。他们大多依赖于翻译的页面文件，这是我想要避免的。
所以我决定通过集成 i18next 来手动完成，因为它已经在其他一些相关项目中使用了。

对于这个例子，让我们假设我们想要创建一个网站，我们有一个主页和一些不同城市的商店页面。

## 页&片段

我们的方法是:

*   仅使用本地化路径以编程方式创建页面
*   在`createPages`中，使用 i18next 加载渲染一个页面所需的所有翻译
*   用`pageContext`传递语言和翻译资源
*   将模板包裹在`gatsby-ssr`和`gatsby-browser`中的`<I18nextProvider>`中。资源从`pageContext`加载。

因此，让我们从删除 pages 目录中的所有文件开始。然后，我们想要为所有语言创建起始页，所以我们转到`gatsby-node.js`。

首先，我们定义所有可用的语言(在这个例子中是英语和德语)。

```
const allLanguages = ["en", "de"] 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们需要一个助手函数，为一个或多个数据集创建所有语言页面。对于每个数据集，它将给定的名称空间加载到 i18next 实例中(1)，然后使用给定的回调来生成定义，以创建 Gatsby 页面(2)。Gatsby 页面将接收语言和加载的语言键以及回调返回的定义(3)。然后，我们映射所有的结果定义，包括最终的 URL 和语言，以生成替代链接(4)。最后，我们为所有的定义创建页面(5)。

```
const buildI18nPages = async (
  inputData,
  pageDefinitionCallback,
  namespaces,
  createPage
) => {
  if (!Array.isArray(inputData)) inputData = [inputData]
  await Promise.all(
    inputData.map(async ipt => {
      const definitions = await Promise.all(
        allLanguages.map(async language => {
          const i18n = await createI18nextInstance(language, namespaces) // (1)
          const res = pageDefinitionCallback(ipt, language, i18n) // (2)
          res.context.language = language
          res.context.i18nResources = i18n.services.resourceStore.data // (3)
          return res
        })
      )

      const alternateLinks = definitions.map(d => ({
        // (4)
        language: d.context.language,
        path: d.path,
      }))

      definitions.forEach(d => {
        d.context.alternateLinks = alternateLinks
        createPage(d) // (5)
      })
    })
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们现在创建我们的第一页！

```
 const homeTemplate = path.resolve(`src/templates/Home.js`)
  await buildI18nPages(
    null,
    (_, language) => ({
      path: "/" + language, // (1)
      component: homeTemplate,
      context: {},
    }),
    ["common", "home"],
    createPage
  )

  const shopTemplate = path.resolve(`src/templates/Shop.js`)
  const shops = await graphql(`
    query Shop {
      allSanityShop {
        edges {
          node {
            id
            _rawSlug
          }
        }
      }
    }
  `)
  await buildI18nPages(
    shops.data.allSanityShop.edges,
    ({ node }, language, i18n) => ({
      path: `/${language}/${i18n.t("common:shopSlug")}/${
        node._rawSlug[language]
      }`,
      component: shopTemplate,
      context: { shop: node.id },
    }),
    ["common", "shop"],
    createPage
  ) 
```

Enter fullscreen mode Exit fullscreen mode

查看第一部分，在`/en`和`/de`上创建每种语言的主页。我们加载页面的`common`和`home`名称空间。然后，我们查询所有商店的健全性，并使用我们的助手函数来创建每个商店的语言版本。这一次，我们创建了一个本地化的 slug(例如，英语的`/en/buy-stuff-in/cologne`和德语的`/de/sachen-kaufen-in/koeln`)。这里构建 slug 的方式是完全任意的，当然，你可以使用任何逻辑。

要使用`i18n.t`,我们需要创建名称空间文件`src/locales/(de|en)/common.json`,并插入`shop-slug`的翻译，更多信息参见 i18next 文档。

## 用 I18nextProvider 包装内容

为了翻译模板中的代码片段，我们需要将页面包装在一个`<I18nextProvider>`中。这可以在`wrapPageElement`中完成，在`gatsby-ssr.js`和`gatsby-browser.js`环境中都可以。因此，我们创建了一个包装器函数，它提取我们在`pageContext` (1)中传递的数据，用我们的数据实例化 i18next，并将内容包装在一个提供者中。这也使用头盔(2)添加了备用链接和 lang 属性。我们还为链接创建了一个上下文，所以我们可以在应用程序的任何地方放置一个上下文敏感的语言切换器。

```
export const AlternateLinksContext = React.createContext([])

export function wrapWithI18nProvider({ element, props }) {
  const i18n = i18next
    .createInstance({
      lng: props.pageContext.language,
      interpolation: { escapeValue: false },
      initImmediate: false,
      resources: props.pageContext.i18nResources,
    })
    .use(ReactI18next.initReactI18next)
  // noinspection JSIgnoredPromiseFromCall
  i18n.init()
  return (
    <ReactI18next.I18nextProvider i18n={i18n}>
      <AlternateLinksContext.Provider
        value={props.pageContext && props.pageContext.alternateLinks}
      >
        {
          <Helmet htmlAttributes={{ lang: props.pageContext.language }}>
            {props.pageContext &&
              props.pageContext.alternateLinks &&
              props.pageContext.alternateLinks.map(link => (
                <link
                  rel="alternate"
                  hrefLang={link.language}
                  href={link.path}
                />
              ))}
          </Helmet>
        }
        {element}
      </AlternateLinksContext.Provider>
    </ReactI18next.I18nextProvider>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

在`gatsby-ssr.js`和`gatsby-browser.js`中，我们简单地使用了这个函数:

```
const wrapWithI18nProvider = require("./src/components/wrapWithI18nProvider")
  .wrapWithI18nProvider

exports.wrapPageElement = wrapWithI18nProvider 
```

Enter fullscreen mode Exit fullscreen mode

## 优化理智 I18n

当使用所建议的方法来实现健全性 I18n 时，您最终会得到一个如下所示的 graphql 模式:

```
type SanityShop {
    _rawName: JSONObject
    _rawSlug: JSONObject
    name: SanityLocaleString
    slug: sanityLocaleString
}

type SanityLocaleString {
    en: String
    de: String
} 
```

Enter fullscreen mode Exit fullscreen mode

既不使用`_rawName`也不使用`name`,我们只能获取基于变量的语言，不幸的是，这是在 Gatsby 中定制查询的唯一方式。但是我们可以稍微扩展一下这个模式，在`gatsby-node.js`中添加一个解决这个问题的解析器。

```
exports.createResolvers = ({ createResolvers }) => {
  createResolvers({
    SanityLocaleString: {
      translate: {
        type: `String!`,
        args: { language: { type: "String" } },
        resolve: (source, args) => {
          return source[args.language] || source["en"]
        },
      },
    },
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

这个解析器允许我们像这样为商店页面编写一个查询，所以我们只获得当前语言的值。

```
export const query = graphql`
  query Shop($shop: String, $language: String) {
    sanityShop(id: { eq: $shop }) {
      name {
        translate(language: $language)
      }
    }
  }
` 
```

Enter fullscreen mode Exit fullscreen mode

通过使用以上两种技巧，我们可以确保将发送给客户机的数据减少到所需的语言和名称空间。

## 重定向到正确的语言

当用户访问页面时，我们希望将他转到一种语言，因为现在，我们在根目录中没有任何东西可以显示。Gatsby 没有服务器，所以我们无法执行任何服务器端重定向(至少没有动态语言检测)。但是 Gatsby 为我们提供了一种方法来创建重定向，并将其实现推送到插件中(例如 netlify 插件)。在真实的场景中，我会将不同的 TLD 重定向到主 TLD 上的不同语言(`example.com`->-`example.com/en`、`example.de`->-`example.com/de/`)。所以在`createPages`中，我们可以设置:

```
 createRedirect({ fromPath: "/", toPath: "/en", isPermanent: true }) 
```

Enter fullscreen mode Exit fullscreen mode

我们当然也可以创建一个索引页面来检测客户端的语言。

## 错误页面

当出现问题，用户访问到一个不存在的 URL 时，我们可以提供一个 404 页面。通过在语言目录中生成 404.html，就像我们在主页中所做的那样，我们可以设置重定向来使用那些页面，以防找不到其他内容:

```
 allLanguages.forEach(language =>
    createRedirect({
      fromPath: `/${language}/*`,
      toPath: `/${language}/404`,
      statusCode: 404,
    })
  ) 
```

Enter fullscreen mode Exit fullscreen mode

你可以在 GitHub 上找到这 404 页的代码，但是大部分和索引页一样，只是内容不同。

## 一个页面模板

我们在 templates 文件夹中创建的页面看起来像普通的 Gatsby 页面，除了它们可以访问上下文中的语言。

```
const ShopPage = ({ data }) => {
  const { t } = useTranslation("shop")
  return (
    <Layout>
      <SEO title="Shop" />
      <h1>{t("welcome", { city: data.sanityShop.name.translate })}</h1>
    </Layout>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

`src/locales/en/shop.json` :

```
{  "welcome":  "Welcome to our Shop in {{city}}"  } 
```

Enter fullscreen mode Exit fullscreen mode

该页面打印一条翻译后的消息，欢迎用户来到特定的商店。它使用 i18next 插值将消息放入字符串中。

## 网站地图

`gatsby-plugin-sitemap`允许我们设置一个自定义的查询和转换函数，所以我们可以查询额外的`pageContext`并相应地设置站点地图数据。所以我们把这个添加到我们的插件配置中:

```
{
      resolve: `gatsby-plugin-sitemap`,
      options: {
        exclude: ["/404", "/*/404"],
        query: `
        {
          site {
            siteMetadata {
              siteUrl
            }
          }

          allSitePage {
            edges {
              node {
                path
                context {
                  alternateLinks {
                    language
                    path
                  }
                }
              }
            }
          }
      }`,
        serialize: ({ site, allSitePage }) =>
          allSitePage.edges.map(edge => {
            return {
              url: site.siteMetadata.siteUrl + edge.node.path,
              changefreq: `daily`,
              priority: 0.7,
              links:
                edge.node.context.alternateLinks &&
                edge.node.context.alternateLinks.map(link => ({
                  lang: link.language,
                  url: site.siteMetadata.siteUrl + link.path,
                })),
            }
          }),
      },
    }, 
```

Enter fullscreen mode Exit fullscreen mode

## 演示

你可以在这里找到这一切的工作实例:
[https://github . com/johannesspohr/Gatsby-starter-sanity-i18 下一个](https://github.com/johannesspohr/gatsby-starter-sanity-i18next)T3【预览】可以在这里看到:
[https://gatsby-starter-i18next-sanity.netlify.com/](https://gatsby-starter-i18next-sanity.netlify.com/)

请随意评论你和盖茨比在 i18n 上的经历！