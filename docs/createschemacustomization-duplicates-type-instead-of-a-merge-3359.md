# ` createSchemaCustomization `复制类型而不是合并。

> 原文：<https://dev.to/philj0st/createschemacustomization-duplicates-type-instead-of-a-merge-3359>

我想通过`createTypes` API 补充我的推断类型，比如:

```
exports.createSchemaCustomization = ({ actions }) => {
  const { createTypes } = actions
  const typeDefs = `
    type SiteSiteMetadataSecondaryNavItems implements Node @infer {
      childItems: [SiteSiteMetadataSecondaryNavItemsChildItems]
    }
    type SiteSiteMetadataSecondaryNavItemsChildItems @infer {
      title: String
      icon: String
      href: String
    }
  `
  createTypes(typeDefs)
} 
```

它非常接近文档中的 Frontmatter 片段，但是我得到:

```
Error: Schema must contain uniquely named types but contains multiple types named "SiteSiteMetadataSecondaryNavItems". 
```

`"gatsby": "^2.13.8",`