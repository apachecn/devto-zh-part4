# 如何创建只有一些可扩展行的虚拟化数据表

> 原文：<https://dev.to/dasdaniel/how-to-create-a-vuelify-data-table-that-has-only-some-expandable-rows-1m9m>

使用 **vuetify v2.0.0**

要使某些项目可扩展，请使用`data-table-expand`插槽。该插槽提供了以下道具。

```
{
  item: any
  select: (v: boolean) => void
  isSelected: boolean
  expand: (v: boolean) => void
  isExpanded: boolean
  headers: TableHeader[]
} 
```

Enter fullscreen mode Exit fullscreen mode

使用`item`对象和`expand`函数，您可以添加定制的 expend 功能。

```
<template>
  <v-data-table
    :headers="myHeaders"
    :items="myItems"
    :single-expand="true"
    show-expand
  >
    <!-- expand column -->
    <template v-slot:item.data-table-expand="{ item, isExpanded, expand }">
      <v-btn @click="expand(true)" v-if="item.canExpand && !isExpanded">Expand</v-btn>
      <v-btn @click="expand(false)" v-if="item.canExpand && isExpanded">close</v-btn>
    </template>

    <!-- expand item/row -->
    <template v-slot:expanded-item="{ headers, item }">
      <td :colspan="headers.length">
        <pre>{{item}}</pre>
      </td>
    </template>
  </v-data-table>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

`items`对象使用一个`canExpand`布尔值来确定它是否可以扩展。

默认情况下，Vuetify 向 expand `td`元素添加一个`text-start`类。要更改对齐方式，请通过添加一个`align`属性来更新该列的标题对象。

可用值有:`'start' | 'center' | 'end'`

扩展列总是第一个，如果你想让它最后，你需要添加一个自定义列。

步骤:

1.  从`v-data-table`上移除`show-expand`支柱，我们不再使用它。

2.  将`:expanded.sync="expanded"` prop 添加到数据表中，将`expanded:[]`变量添加到组件`data`中。

3.  在`myHeaders`中增加一个新列`{ text: '', value: 'expand', align:'end' },`。

4.  为新列添加行槽。因为 row slot 没有提供`expand`函数，所以需要使用`expanded`变量来切换选择。

```
<template v-slot:item.expand="{ item }">
  <v-btn x-small @click="expanded = [item]" v-if="item.canExpand && !expanded.includes(item)">compare</v-btn>
  <v-btn x-small @click="expanded = []" v-if="item.canExpand && expanded.includes(item)">close</v-btn>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

更多信息请点击这里:[https://vuetifyjs.com/en/components/data-tables#api](https://vuetifyjs.com/en/components/data-tables#api)