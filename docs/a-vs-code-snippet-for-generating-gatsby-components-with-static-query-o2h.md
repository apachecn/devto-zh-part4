# 用静态查询生成 Gatsby 组件的 VS 代码片段

> 原文：<https://dev.to/imed/a-vs-code-snippet-for-generating-gatsby-components-with-static-query-o2h>

不得不用 Gatsby 的`StaticQuery`为 React 组件重新编写相同的样板代码会令人厌烦。这就是为什么我最近为 VS 代码做了一个小片段，以便自动化这个过程。

转到`File > Preferences > User Snippets`(或代码>首选项>用户片段`，在 MacOS 上),选择 JavaScript，并将以下片段粘贴到花括号内:

``JSON
"Gatsby Static Query": {
"prefix": "gatstaq",
"body": [
"import React from 'react'",
"import { StaticQuery, graphql } from 'gatsby'\n",
"const ${1/(.*)/${1:/capitalize}/} = ({ data }) => (\n\t$0\n)\n",
"const ${1:page}Query = graphql`\ n \ t \ n`\n",
"export default () => (\n\t<StaticQuery query={${1:page}Query} render={data => <${1/(.*)/${1:/capitalize}/} data={data} />} />\n)\n"
]
}` T2】`

 `*最初发布在我的博客上:[Gatsby static query VS 代码片段](https://imedadel.me/gatsby-staticquery-vs-code-snippet)T3】*`