# Gatsby 中 Wordpress 菜单来源完整指南

> 原文：<https://dev.to/bouthouri/complete-guide-to-wordpress-menu-sourcing-in-gatsby-h76>

使用 Gatsby 插件 gatsby-source-wordpress，我们可以从 wordpress 网站上获取几乎所有东西。默认情况下只有两样东西没有，菜单和自定义选项。
这篇博文是关于菜单的。

## 1。准备菜单

正如我刚才提到的，默认情况下，Wordpress 菜单在 Wordpress REST api 上是不可用的，所以第一步是使它们可见。

我们可以用一个 Wordpress 插件 [WP REST API 菜单](https://wordpress.org/plugins/wp-api-menus/)轻松做到这一点，当你想直接从你的网站安装它时，你找不到它。

你需要下载它并使用 WordPress 管理插件上传，如果你不知道如何上传，这里有一个到教程的链接。
现在你已经安装了插件，你的菜单在 rest api 上是可用的。

> WP-REST-API V2 菜单不是安装的好插件。

## 2。请求:

安装插件后，如果您运行`gatsby develop`,一个新的 GraphQL 请求将可用:

*   allWordpressWpApiMenusMenus 获取可用的菜单
*   allWordpressWpApiMenusMenusItems 获取菜单项
*   获取可用菜单位置的 allWordpressWpApiMenusMenuLocations

## 3。有用的请求:

有很多有用的请求，但在我们的例子中，我们只需要检索菜单和它们的项目“内容”，这就是对它的请求。

```
{
  allWordpressWpApiMenusMenusItems {
    edges{
      node {
        slug
        name
        items {
          title
          url
        }
      }
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你的菜单包含子菜单，使用 wordpress_childer 属性

```
{
  allWordpressWpApiMenusMenusItems {
    edges{
      node {
        slug
        name
        items {
          title
          url
          wordpress_children {
            title
            url
          }
        }
      }
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你想检索一个特定的菜单，使用这样的过滤器

```
{
  allWordpressWpApiMenusMenusItems(filter: {slug: {eq: "sidebar-menu"}}) {
    edges{
      node {
        slug
        name
        items {
          title
          url
          object_slug
          wordpress_children {
            title,
            url
          }
        }
      }
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 4。获取菜单

Gatsby 组件中的完整代码:
现在我们已经有了所有需要的东西，这是一个显示 Wordpress 菜单的小组件

```
import React from "react"
import { StaticQuery, graphql } from "gatsby"

export default () => (
  <StaticQuery
    query={graphql`
      query {
        allWordpressWpApiMenusMenusItems(
          filter: { slug: { eq: "sidebar-menu" } }
        ) {
          edges {
            node {
              slug
              name
              items {
                title
                url
                object_slug
                wordpress_children {
                  title
                  url
                }
              }
            }
          }
        }
      }
    `}
    render={data => {
      return (
        <nav className="menu">
          <ul className="nav flex-column">
            {data &&
              data.allWordpressWpApiMenusMenusItems &&
              data.allWordpressWpApiMenusMenusItems.edges &&
              data.allWordpressWpApiMenusMenusItems.edges[0] &&
              data.allWordpressWpApiMenusMenusItems.edges[0].node &&
              data.allWordpressWpApiMenusMenusItems.edges[0].node.items &&
              data.allWordpressWpApiMenusMenusItems.edges[0].node.items.map(
                prop => {
                  return (
                    <li className="nav-item">
                      <a
                        className="nav-link active"
                        href={prop.url}
                        alt={prop.title}
                      >
                        {prop.title}
                      </a>
                      <div className="sub-menu">
                        {prop &&
                          prop.wordpress_children &&
                          prop.wordpress_children.map(child => {
                            console.log("child ", child)

                            return (
                              <a
                                className="dropdown-item"
                                href={child.url}
                                alt={child.title}
                              >
                                {child.title}
                              </a>
                            )
                          })}
                      </div>
                    </li>
                  )
                }
              )}
          </ul>
        </nav>
      )
    }}
  />
) 
```

Enter fullscreen mode Exit fullscreen mode

## 5。正确的网址

如果你的 Wordpress 域名和你的 Gatsby 网站不一样，别忘了替换它们。在 gatsby-config.js 中把这个放到插件声明中

```
searchAndReplaceContentUrls:  {  sourceUrl:  "http://localhost:8080",  replacementUrl:  "http://localhost:8000",  } 
```

Enter fullscreen mode Exit fullscreen mode

下面是完整的例子:

```
{  resolve:  "gatsby-source-wordpress",  options:  {  baseUrl:  "http://localhost:8080/",  protocol:  "http",  hostingWPCOM:  false,  useACF:  false,  excludedRoutes:  ["**/settings",  "**/themes",  "**/users/me"],  verboseOutput:  true,  searchAndReplaceContentUrls:  {  sourceUrl:  "http://localhost:8080",  replacementUrl:  "http://localhost:3000",  },  },  } 
```

Enter fullscreen mode Exit fullscreen mode

## 6。结论

这并不总是一个好的方法，有时甚至有些矫枉过正，但是了解 Wordpress 菜单的一切以及它们在 Gatsby 中的使用还是很不错的。你可能有不同的观点或不同的方法。请在评论中分享你的观点。