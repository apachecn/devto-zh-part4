# 如何在你的 Gatsby 站点中使用 Drupal 段落

> 原文：<https://dev.to/ascorbic/how-to-use-drupal-paragraphs-in-your-gatsby-site-104l>

如果您使用 Drupal 作为 Gatsby 的数据源，那么您确实应该使用段落模块。这是建设网站最有效的方式之一。它允许你创建“段落类型”,编辑可以添加和重新排序来组合页面。段落可以是简单的类型，如 HTML 块或图像列表，也可以是包含许多不同字段的复杂类型。大多数文档都与在 Drupal 主题中使用它有关，但是它也很好地映射到 React 组件。您可以创建自定义组件，然后定义匹配的段落类型，以便编辑人员将它们添加到页面中。这可以给编辑提供比无头 CMS 更大的灵活性。

弄清楚如何设置可能有点棘手，但希望这篇文章能帮助你用 Drupal 段落和 Gatsby 构建漂亮的基于组件的站点。

我不打算详细介绍如何设置 Drupal。我将假设您已经安装了[段落](https://git.drupalcode.org/project/paragraphs/)并启用了段落和`paragraphs_demo`模块。我创建了一个名为“分段文章”的内容类型，其中有一个名为`field_paragraphs_demo`的字段，我将在这里使用它。

设置好 Drupal 并填充了一些内容后，在 gatsby 中添加并启用 [gatsby-source-drupal](https://github.com/gatsbyjs/gatsby/tree/master/packages/gatsby-source-drupal) 。启动 Gatsby 服务器，看看 GraphiQL。您应该在浏览器中看到您的段落页面类型。尝试一个简单的查询，看看你会得到什么:

```
{  allNodeParagraphedContentDemo  {  nodes  {  relationships  {  field_paragraphs_demo  {  __typename  }  }  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

您应该会看到一系列页面，其中包含各种类型的段落。在我的例子中，我得到了如下结果:

```
{  "data":  {  "allNodeParagraphedContentDemo":  {  "nodes":  [  {  "relationships":  {  "field_paragraphs_demo":  [  {  "__typename":  "paragraph__image_text"  },  {  "__typename":  "paragraph__images"  },  {  "__typename":  "paragraph__text"  }  ]  }  },  {  "relationships":  {  "field_paragraphs_demo":  [  {  "__typename":  "paragraph__image_text"  },  {  "__typename":  "paragraph__text"  },  {  "__typename":  "paragraph__text"  },  {  "__typename":  "paragraph__image_text"  }  ]  }  }  ]  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

那是两页，第一页有三段，第二页有四段。你应该注意到每种段落类型都不止一种。

在`gatsby-node.js`中，你首先需要创建页面:

```
const path = require("path");

exports.createPages = async ({ graphql, actions }) => {
    const { createPage } = actions;
    const tpl = path.resolve(`src/templates/paragraph.js`);

    //   Adjust these field names as needed
    const result = await graphql(`
        {
            paragraphPages: allNodeParagraphedContentDemo {
                edges {
                    node {
                        fields {
                            slug
                        }
                        drupal_internal__nid
                    }
                }
            }
        }
    `);
    result.data.paragraphPages.edges.forEach(({ node }) => {
        createPage({
            path: node.fields.slug,
            component: tpl,
            context: {
                slug: node.fields.slug
            }
        });
    });
};

exports.onCreateNode = ({ node, getNode, actions }) => {
    const { createNodeField } = actions;
    // Use the type of your own paragraph page
    if (node.internal.type === `node__paragraphed_content_demo`) {
        const slug = `/pages/${node.drupal_internal__nid}/`;
        createNodeField({
            node,
            name: `slug`,
            value: slug
        });
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止，这类似于`using-drupal`演示。有趣的部分开始于页面模板。我把我的叫做`paragraph.js`。您可以在其中使用页面查询。

```
// paragraph.js
import React from "react";
import { graphql } from "gatsby";
import Layout from "../components/layout";

const PageTemplate = ({ data }) => {
    const paragraphs = data.page.relationships.paragraphs.map(paragraph => (
        <li>{paragraph.type}</li>
    ));

    return (
        <Layout>
            <h1>{data.page.title}</h1>
            <ul>{paragraphs}</ul>
        </Layout>
    );
};

export default PageTemplate;

export const pageQuery = graphql`
    query($slug: String!) {
        page: nodeParagraphedContentDemo(fields: { slug: { eq: $slug } }) {
            id
            title
            relationships {
                paragraphs: field_paragraphs_demo {
                    type: __typename
                }
            }
        }
    }
`; 
```

Enter fullscreen mode Exit fullscreen mode

在加载页面之前，您可以创建索引页面来给出一个简单的链接列表:

```
// index.js
import React from "react";
import { Link, useStaticQuery, graphql } from "gatsby";

import Layout from "../components/layout";
import SEO from "../components/seo";

const IndexPage = () => {
    const data = useStaticQuery(graphql`
        query PageQuery {
            paragraphPages: allNodeParagraphedContentDemo {
                nodes {
                    title
                    fields {
                        slug
                    }
                }
            }
        }
    `);
    return (
        <Layout>
            <ul>
                {data.paragraphPages.nodes.map(node => (
                    <li>
                        <Link to={node.fields.slug}>{node.title}</Link>
                    </li>
                ))}
            </ul>
        </Layout>
    );
};

export default IndexPage; 
```

Enter fullscreen mode Exit fullscreen mode

如果你加载首页，它会给你一个很好的页面链接列表。试着点击一个来查看页面。

现在这个页面只显示了段落类型的列表。有趣的部分是将它映射到组件。

我的示例有以下段落类型:

*   段落 _ _ 图像 _ 文本
*   段落 _ _ 图片
*   第 _ _ 段案文

这些与 Drupal 中创建的段落类型相匹配。您需要为每种段落类型创建一个组件。从文本段落开始。Drupal 为此提供了经过消毒的 HTML，所以我们可以直接设置它(但是“很危险”)。您需要更新页面模板中的查询来获取段落的数据。如果通过使用 GraphQL 片段将查询与组件放在一起，跟踪起来会容易得多。

```
// TextParagraph.js
import React from "react";
import { graphql } from "gatsby";

export const TextParagraph = ({ node }) => (
    <div
        style={{
            borderStyle: "solid",
            marginBottom: 2
        }}
    >
        <div dangerouslySetInnerHTML={{ __html: node.text.processed }} />
    </div>
);

export const fragment = graphql`
    fragment ParagraphText on paragraph__text {
        id
        text: field_text_demo {
            format
            processed
            value
        }
    }
`; 
```

Enter fullscreen mode Exit fullscreen mode

现在，您可以在模板的页面查询中使用这个片段:

```
export const pageQuery = graphql`
    query($slug: String!) {
        page: nodeParagraphedContentDemo(fields: { slug: { eq: $slug } }) {
            id
            relationships {
                paragraphs: field_paragraphs_demo {
                    type: __typename
                    ...ParagraphText
                }
            }
            title
        }
    }
`; 
```

Enter fullscreen mode Exit fullscreen mode

如果您在页面中记录数据，您应该看到文本段落现在有一个带有文本字段的对象。

顺便说一下，这种将 GraphQL 片段与使用数据的组件放在一起的模式也可以用在其他地方，而不仅仅是这些段落。例如，如果您有一个组件需要特定大小的图像，您可以在它旁边包含一个片段，该片段具有 ImageSharp 查询以返回正确的图像大小，然后您可以在页面查询中使用它。

现在，您已经有了文本段落的正确数据，以及显示它的组件。现在，您可以为文本+图像段落类型创建一个类似的组件。这一次我们需要使用 ImageSharp 查询来获取图像。

```
// ImageAndTextParagraph.js
import React from "react";
import Img from "gatsby-image";
import { graphql } from "gatsby";

export const ImageAndTextParagraph = ({ node }) => (
    <figure>
        <Img fixed={node.relationships.image.localFile.childImageSharp.fixed} />
        <figcaption dangerouslySetInnerHTML={{ __html: node.text.processed }} />
    </figure>
);

export const fragment = graphql`
    fragment ParagraphImageText on paragraph__image_text {
        id
        image: field_image_demo {
            alt
        }
        text: field_text_demo {
            format
            processed
            value
        }
        relationships {
            image: field_image_demo {
                id
                localFile {
                    childImageSharp {
                        fixed(width: 400) {
                            ...GatsbyImageSharpFixed_withWebp
                        }
                    }
                }
            }
        }
    }
`; 
```

Enter fullscreen mode Exit fullscreen mode

为了简洁起见，我省略了这些组件中的所有空检查，但是您应该检查每个字段是否存在(如果您的环境支持，我推荐使用[可选链接](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Optional_chaining))。这对图像尤其重要。

将`ParagraphImageText`片段添加到页面查询:

```
// paragraph.js
export const pageQuery = graphql`
    query($slug: String!) {
        page: nodeParagraphedContentDemo(fields: { slug: { eq: $slug } }) {
            id
            relationships {
                paragraphs: field_paragraphs_demo {
                    type: __typename
                    ...ParagraphText
                    ...ParagraphImageText
                }
            }
            title
        }
    }
`; 
```

Enter fullscreen mode Exit fullscreen mode

Images 段落是类似的，但是有几个图像的数组。你可以为类似旋转木马的东西创建一个类似的段落。

```
// ImagesParagraph.js
import React from "react";
import Img from "gatsby-image";
import { graphql } from "gatsby";

export const ImagesParagraph = ({ node }) => {
    const { images } = node.relationships;
    return (
        // Carousel like it's 1999! (Seriously though, never do this)
        <marquee>
            {images.map(image => (
                <Img fixed={image.localFile.childImageSharp.fixed} />
            ))}
        </marquee>
    );
};

export const fragment = graphql`
    fragment ParagraphImages on paragraph__images {
        id
        relationships {
            images: field_images_demo {
                id
                localFile {
                    childImageSharp {
                        fixed(width: 200, height: 200) {
                            ...GatsbyImageSharpFixed
                        }
                    }
                }
            }
        }
    }
`; 
```

Enter fullscreen mode Exit fullscreen mode

现在你已经有了所有段落类型的组件，你只需要解决如何显示正确的组件。我为此使用了一个小助手函数:

```
// paragraphHelpers.js
import React from "react";
import { ImageAndTextParagraph } from "./components/ImageAndTextParagraph";
import { TextParagraph } from "./components/TextParagraph";
import { ImagesParagraph } from "./components/ImagesParagraph";

const components = {
    paragraph__image_text: ImageAndTextParagraph,
    paragraph__text: TextParagraph,
    paragraph__images: ImagesParagraph
};

export const getParagraph = node => {
    if (components.hasOwnProperty(node.type)) {
        const ParagraphComponent = components[node.type];
        return <ParagraphComponent key={node.id} node={node} />;
    }
    return <p key={node.id}>Unknown type {node.__typename}</p>;
}; 
```

Enter fullscreen mode Exit fullscreen mode

`components`对象将段落名称从 Drupal 映射到组件。在这个例子中，如果有未识别的类型，我将返回一个警告，但是在生产中，您可能只想记录一些东西。

现在你可以使用这个函数将你的数据转换成组件:

```
import React from "react";
import { graphql } from "gatsby";
import Layout from "../components/layout";

import { getParagraph } from "../paragraphHelpers";

export const PageTemplate = ({ data }) => {
    const paragraphs = data.page.relationships.paragraphs.map(getParagraph);

    return (
        <Layout>
            <h1>{data.page.title}</h1>
            {paragraphs}
        </Layout>
    );
};

export default PageTemplate;

export const pageQuery = graphql`
    query($slug: String!) {
        page: nodeParagraphedContentDemo(fields: { slug: { eq: $slug } }) {
            id
            relationships {
                paragraphs: field_paragraphs_demo {
                    type: __typename
                    ...ParagraphText
                    ...ParagraphImageText
                    ...ParagraphImages
                }
            }
            title
        }
    }
`; 
```

Enter fullscreen mode Exit fullscreen mode

这将给你所有的段落以正确的顺序和正确的成分。

这些例子没什么特别的，因为我们使用的是基本的默认段落类型，您可以很容易地将它们添加到普通页面中。当您创建自定义段落类型以匹配您希望编辑器能够添加到页面的特定自定义组件时，这是最强大的。一个地图组件怎么样，用一个自定义的段落来设置边界？或者一个时事通讯注册，用一个自定义的段落来选择邮件列表？

这里我们没有处理嵌套段落，或者使用段落库模块。这些变得有点复杂，但仍然可以使用相同的系统进行建模:您只需要更深入地了解实际数据。