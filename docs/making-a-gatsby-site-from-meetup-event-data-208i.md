# 从 Meetup 事件数据制作 Gatsby 网站

> 原文：<https://dev.to/andrioid/making-a-gatsby-site-from-meetup-event-data-208i>

作为奥尔堡前端会议的演讲准备的一部分，我打算浏览一个新的 Gatsby 站点，并使用 [gatsby-source-meetup](https://www.gatsbyjs.org/packages/gatsby-source-meetup/?=meetup) 导入即将到来的事件。

想象一下我有多惊讶，当我发现 Meetup 已经停止发布 API 密匙的时候。

## 盖茨比-来源-ical

Gatsby 有一个精彩的源码插件集合，其中一个恰好是 [gatsby-source-ical](https://www.gatsbyjs.org/packages/gatsby-source-ical/?=ical) 。我会把它交给 Meetup 支持；他们给我发了一个有用的链接，表明我可能不需要 API，因为他们有一个可用的日历提要。

## 查找日历导出

首先，在 [Meetup](https://www.meetup.com) 上找到你的群，点击“活动”、“日历”，在底部有订阅链接(如果你已经登录)。

对我们组来说，网址是:“[https://www.meetup.com/Aalborg-Frontend/events/ical/](https://www.meetup.com/Aalborg-Frontend/events/ical/)”

## 添加插件

```
yarn add gatsby-source-ical 
```

Enter fullscreen mode Exit fullscreen mode

### 盖茨比-config.js

```
// In your gatsby-config.js
module.exports = {
    plugins: [
        // You can have multiple instances of this plugin
        // to read source nodes from different remote files
        {
            resolve: `gatsby-source-ical`,
            options: {
                name: `events`,
                url: `https://web-standards.ru/calendar.ics`
            }
        }
    ]
} 
```

Enter fullscreen mode Exit fullscreen mode

### 查询数据

```
{
    allIcal {
        edges {
            node {
                start
                end
                summary
            }
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 为什么？

我把这些放在一起，以防有人用 Gatsby 创建了一个 meetup 网站，并对 API 的变化感到沮丧。