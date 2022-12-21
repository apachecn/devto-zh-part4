# Gridsome 0.7 发布！

> 原文：<https://dev.to/lpellis/gridsome-0-7-released-2kn1>

Gridsome 是一个基于 JAMstack 的新的静态站点生成器，它是我在 https://loftie.com/ T2 的博客的底层软件。如果你听说过盖茨比，那么 Gridsome 就是 vue.js 的等价物，他们刚刚发布了一个新版本，有一些很酷的改进。

### 新模式 API

这对我来说是最大的进步。Gridsome 曾经简单地从启动时传递的数据中推断出 GraphQL 模式，如果所有数据都存在，这种方法就能很好地工作，但是我开始遇到一些嵌套条目(例如标签)没有被正确发现的问题。

使用模式 API，您现在可以自己定义字段:

```
api.loadSource(({ addSchemaTypes }) => {
  addSchemaTypes(`
    type Post implements Node {
      title: String
    }
  `)
}) 
```

您也不必指定所有的字段，Gridsome 仍然可以自动推断出您知道将会出现的字段，并且只指定更深的字段。为此，只需传递 [@infer](https://gridsome.org/docs/schema-api/#infer) 扩展。

```
type Post implements Node @infer {
    title: String
} 
```

### 其他变化

*   Vue 备注插件-让你添加 vue 组件到降价文件-
*   新模板配置——指定模板路由的更一致的方式
*   自定义 app . vue——如果你想在`<router-view>`周围添加一个`<transition>`组件——或者更多，请点击查看完整列表

在[https://gridsome.org/](https://gridsome.org/)亲自看看 Gridsome