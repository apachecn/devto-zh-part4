# 程序化页面生成

> 原文：<https://dev.to/stephencweiss/programmatic-page-generation-43h>

# 程序化页面生成

好吧，我已经建立到这一点！是时候使用 Gatsby 以编程方式为博客生成新页面了！

以前我写过关于配置 Gatsby 的`gatsby-source-filesystem`插件和编写接受变量的 GraphQL 查询。这篇文章将结合这两个教训，为我的文件系统中的每篇文章生成遵循简单布局的页面。

这篇文章开始假设一个配置好的`gatsby-source-filesystem`，并且我已经准备好设置我的`gatsby-node`文件来创建页面。

首先，`gatsby-node`是 Gatsby 在根目录中寻找的几个文件之一(`gatsby-config`是另一个例子)。

Gatsby 附带了一套内置插件。要使用它们中的任何一个，您可以从`gatsby-config`文件中导出它们。我将使用`createPages`异步操作。

例如:

```
exports.createPages = async ({ actions, graphql, reporter }) => {
  // In node (which this is), graphql is a function that takes a string.
  const result = await graphql(`
    query {
      allMdx {
        nodes {
          frontmatter {
            slug
          }
        }
      }
    }
  `);
  if (result.errors) {
    reporter.panic('failed to create posts', result.errors);
  }

  const posts = result.data.allMdx.nodes;

  posts.forEach(post => {
    const path = post.frontmatter.slug;
    actions.createPage({
      path,
      component: require.resolve('./src/templates/post.js'),
      context: {
        slug: post.frontmatter.slug,
      },
    });
  });
}; 
```

浏览此功能:

1.  我将`result`的值赋给 GraphQL 查询的返回值。返回的结果*是*这个鼻涕虫。注意，在`node`中，GraphQL 查询被包装在`()`中。

我检查错误(`result`返回一个带有`.error`属性的承诺)

*   盖茨比`reporter`是一个内置的控制台日志。`Panic`是错误状态。
    1.  如果我通过了错误检查，那么我知道我至少返回了一些东西，这意味着我可以取出我们的节点。在我迄今为止非常简单的例子中——我有两个:![](img/e54109117797972b72cbed8c837b5536.png)
    2.  因为它是一个数组，所以我现在可以使用一个`forEach`循环遍历它，使用 Gatsby 的内置`actions`生成一个页面。Gatsby 使用 Redux 通过一系列内置动作来管理状态和船只(T2 是其中之一)。⁴

`createPage` action API 接受一个对象和两个可选参数(`plugin`和`actionOptions`)，对于这个简单的用例，这两个参数我都不需要。

我识别的物体有三样东西:

1.  路径——这将是我创建的页面所在的位置
2.  组件——在本例中，是我保存帖子模板的路径引用
3.  上下文——生成组件所需的任何上下文。这个*可以*包括文章的正文和任何资产，然而，在这个例子中，我把它放入文章本身，并把关注点分开一点。

在这一点上，我已经准备好查看作为动作组件的`post`文件。

例如-一个简单的帖子布局可能看起来像:

```
import React from ‘react’;
import { graphql, Link } from ‘gatsby’;
import { MDXRenderer } from ‘gatsby-mdx’;
import Layout from ‘../components/layout’;

export const query = graphql`
  query($slug: String!) {
    mdx(frontmatter: { slug: { eq: $slug } }) {
      frontmatter {
        title
        author
      }
      code {
        body
      }
    }
  }
`;

const PostTemplate = ({ data: { mdx: post } }) => {
  const { frontmatter, code } = post;

  return (
    <Layout>
      <h1>{frontmatter.title}</h1>
      <p style={{fontSize: '0.75rem'}}>
        {`posted by ${frontmatter.author}`}
      </p>
      <MDXRenderer>{code.body}</MDXRenderer>
      <Link to=“/“>Return to home</Link>
    </Layout>
  );
};

export default PostTemplate; 
```

这里有几个注意事项:从`gatsby-node`配置传入的 slug 是我将用来查询生成帖子的数据的上下文。

我把我的博客文章写成`.mdx`文件，并使用`gatsby-mdx`插件解析任何`.mdx`或`.md`文件。这样做的好处是我可以使用`MDXRenderer`轻松渲染文章的主体。

所有这些意味着什么？

每当我向我的文件系统添加一个新的 post，它在节点模块中的 all MDX 查询中返回，它将被传递给 a `createPage`动作，并使用`MDXRenderer`以基本格式呈现:

1.  标题
2.  作者
3.  身体
4.  回到家里。

相当漂亮！

*   [Gatsby-Source-file system 入门| /*代码注释*/](https://www.stephencharlesweiss.com/2019-07-20/gatsby-source-filesystem/)
*   [如何使用变量 GraphQL 查询| /*代码注释*/](https://www.stephencharlesweiss.com/2019-07-24/graphql-variable-queries/)
*   [以编程方式从 data | GatsbyJS 创建页面](https://www.gatsbyjs.org/tutorial/part-seven/#creating-pages)
*   ⁴ [行动|盖茨比](https://www.gatsbyjs.org/docs/actions/)