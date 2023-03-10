# 结合 Apollo 工具和 Gatsby 实现类型安全

> 原文：<https://dev.to/denisaugsburger/combine-apollo-tooling-and-gatsby-for-type-safety-3bpm>

Apollo Tooling 是一个命令行工具，用于从 GraphQL 后端生成静态类型。与 GatsbyJS 结合使用，它可以用来生成 TypeScript 定义和验证查询。为了支持更快、更安全和更可靠的方式来编写这些查询，我强烈推荐使用 Apollo VSCode 插件。它突出显示了您的 graphql 查询，并为您提供了有用的自动完成功能。

## 下载模式并安装 VSCode 插件

CLI 查找配置文件和额外提供的命令行标志。对于特定的任务，可以使用不同的配置文件。首先我们要下载模式，因此您开始您的 gatsby 项目

```
gatsby developer 
```

将配置创建为 apollo.config.download.js

```
module.exports = { 
  client: {    
    service: {      
      name: "gatsby-schema",
      url: "http://localhost:8000/___graphql"
    },
    tagName: "graphql"  
  }
}; 
```

将您的模式下载到 schema.json

```
apollo service:download -c apollo.config.download.js 
```

### 安装 Apollo GraphQL VSCode 插件

安装插件

> 外部安装 apollographql.vscode-apollo

接下来创建 apollo.config.js 文件。这个用于代码生成以及 VSCode 插件。

```
module.exports = {
  client: {    
    addTypename: false,
    includes: [
      "./src/**/*.tsx",
      "./src/**/*.ts",
      "./node_modules/gatsby-transformer-sharp/src/fragments.js",
      "./node_modules/gatsby-source-contentful/src/fragments.js"
    ],    
    service: {      
      name: "gatsby-schema",
      localSchemaFile: "./schema.json"
    },
    tagName: "graphql"
  }
}; 
```

GatsbyJS 使用 graphql 作为查询的默认标记名，而 apollo 使用 gql 作为默认标记名。这使我们有可能将它用于 apollo 客户端。一些 GatsbyJS 插件提供了一些全局 GraphQL 片段，这些片段在您的查询中会用到，但不会被引用。因此，您需要将它们包含在您的 apollo.config.js 中，否则您将得到如下错误

> 未知片段“GatsbyImageSharpFixed”

现在我们可以享受 apollo vscode 插件的自动完成和语法高亮显示，正如你在下面的动画 gif 中看到的。

[![Apollo VS Code Plugin preview Gatsby Query](img/5f37ce8e4da80fd56abaa1da9e955795.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ScTCcmAH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://images.ctfassets.net/tb3nth97hxik/2SFQeDFZLWGuNazwcW8gex/47c9b7f403a2d1a0d6b22407e6caba3c/PreviewAutocomplete.gif)

## 从查询中生成类型脚本定义

最后一步，将提供的模式信息和 GatsbyJS 查询结合起来，为 TypeScript 创建客户端类型定义。

> apollo 客户端:codegen-c Apollo . config . JSON-target typescript _ _ generated _ _

将为 GraphQL 查询的每个页面和组件创建一个新的子目录——它被命名为 __generated__。从那里，您可以导入您的 TypeScript 定义。**提示:**

```
type PageProps<TData> = {
 data: TData;
} & MatchRenderProps<{}>; 
```

对于您的页面，您可以使用泛型创建一个可重用的类型，该类型可以针对横切关注点进行扩展。那么您可以按如下方式使用它:

```
type BlogProps = PageProps<BlogPageQuery>
export const MyPage: React.FC<BlogProps> = ({data}) => {
    return ()
}
export const pageQuery = graphql`
  query BlogPageQuery {
    contentfulImages(name: { eq: “testImage” }) {
      id
      description   
    }
  }
` 
```

在您的组件中，您也可以直接使用通用版本。

```
const data = useStaticQuery<TestPageQuery>(
  graphql`
    query TestPageQuery {
      contentfulImages(name: { eq: "testImage" }) {
        id
        description
        name
        fluid(maxWidth: 1000) {
          ...GatsbyContentfulFluid
        }
      }
    }
  `
) 
```

[![Type Safe GatsbyJS project with apollo code generation](img/53b64a7a50d01b06098f7a9c0d0ca6c7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sFYS63-H--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://images.ctfassets.net/tb3nth97hxik/1aSh6NQaxHDZIZ2RSvl4uB/63f55875cf358c81361ac7bb23799258/PreviewCodeGeneration.gif)

### 电流限制

*   在某些情况下，我们经历了 schema.json 文件在一些更改后没有正确地重新加载到 Apollo VS Code 插件中。因此，重启 VS 代码是必要的
*   空检查可能很广泛，所以最好引入一些帮助函数来获取相关数据
*   通过严格的空值检查，你不能在 gatsby images 中使用内容丰富的图像，因为对空值和未定义值的处理不同