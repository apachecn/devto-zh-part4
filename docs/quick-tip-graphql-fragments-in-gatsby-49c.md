# 快速提示:Gatsby 中的 GraphQL 片段

> 原文：<https://dev.to/joelmturner/quick-tip-graphql-fragments-in-gatsby-49c>

GraphQL 使我们很容易准确地获得给定组件所需的数据。很多时候，我们最终会在多个地方使用同一个查询或查询的一部分。这就是 GraphQL 片段可以帮助我们的地方。

要声明一个片段，我们可以添加`fragment MyAwesomeFragment on BlogPost`。关键字`fragment`告诉 GraphQL 我们正在创建名为`MyAwesomeFragment`的片段。而这一切都是在`BlogPost`型上。

在 joelmturner.com，我在几个不同的地方为画廊查询图片。因此，我可以创建一个查询文件，将重复的部分放入其中，而不是在整个站点的每个变体中重复整个查询。这是一个实际应用的例子。

```
fragment  InstaNodes  on  InstagramContentEdge  {  node  {  id  localImage  {  childImageSharp  {  fluid(maxWidth:  1248,  maxHeight:  1248)  {  ...GatsbyImageSharpFluid  }  }  }  images  {  standard_resolution  {  width  height  url  }  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以在页面查询或静态查询的组件中使用它们。

```
const data = useStaticQuery(graphql`
  query {
    allInstagramContent(
      filter: { tags: { glob: "ink*2017" } },
      sort: { fields: created_time, order: ASC }) {
        edges {
          ...InstaNodes
        }
    }
  }
`) 
```

Enter fullscreen mode Exit fullscreen mode

## 打字

如果您使用 TypeScript，您还可以删除查询文件中片段的类型，这样它们就在一起了。

```
type IInstaNodes = {
  node: {
    id: string;
    localImage: {
      childImageSharp: {
        fluid: FluidObject;
      }
    }
    images: {
      standard_resolution: {
        width: number;
        height: number;
        url: string;
      }
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 奖金

如果您正在进行类似的顶级请求，请添加别名。这是我的 Instagram 请求的一个例子。

```
fragment  inktober2017  on  Query  {  inktober2017:  allInstagramContent(  filter:  {  tags:  {  glob:  "ink*2017"  }  }  sort:  {  fields:  created_time,  order:  ASC  }  )  {  edges  {  ...InstaNodes  }  }  }  fragment  inktober2018  on  Query  {  inktober2018:  allInstagramContent(  filter:  {  tags:  {  glob:  "ink*2018"  }  }  sort:  {  fields:  created_time,  order:  ASC  }  )  {  edges  {  ...InstaNodes  }  }  }  fragment  letterClash  on  Query  {  letterClash:  allInstagramContent(  filter:  {  tags:  {  eq:  "letterclash"  }  }  sort:  {  fields:  created_time,  order:  ASC  }  )  {  edges  {  ...InstaNodes  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以像这样获取数据:

```
const {
    inktober2017: { edges: ink2017Edges = [] } = {},
    inktober2018: { edges: ink2018Edges = [] } = {},
    letterClash: { edges: letterClashEdges = [] } = {},
  } = data 
```

Enter fullscreen mode Exit fullscreen mode

你可以在我的[插图页](https://joelmturner.com/illustration)上看到这个结果。你用的是 graphql 片段还是别名？如果是，他们是如何为你工作的？