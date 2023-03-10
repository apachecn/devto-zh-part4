# 用 TypeScript 建模 Schema.org JSON-LD:一个由四部分组成的故事

> 原文：<https://dev.to/eyassh/modeling-schema-org-json-ld-in-typescript-a-story-in-four-parts-254k>

最近我发表了 [**schema-dts**](https://dev.to/eyassh/schema-dts-schemaorg-linked-data-types--code-generation-544p) ，这是一个开源库，用 TypeScript 对 JSON-LD Schema.org 建模。我想做这个项目的一个重要原因是因为我知道一些 TypeScript 类型系统的特性，比如有区别的类型联合、强大的类型推断、可空性检查和类型交集，它们提供了一个机会来模拟符合 Schema.org 的 JSON-LD 的样子，同时也为开发人员提供了符合人体工程学的完成。

我写了一系列文章，描述了一些非常适合 TypeScript 的类型系统的结构化数据概念，以及那些不适合的概念。

## 1。[使用结构化类型对 Schema.org 类层次结构建模](https://blog.eyas.sh/2019/05/modeling-schema-org-schema-with-typescript-the-power-and-limitations-of-the-typescript-type-system/)

Schema.org JSON-LD 节点对象总是*类型的*(也就是说，它们有一个指向某个 IRI 的`@type`属性——一个描述它的字符串)。给定一个`@type`，你就知道在一个特定对象上定义的所有属性。对象类型相互继承。比如 Schema.org 的`Thing`有一个属性叫`name`,`Person`是`Thing`的子类，定义了`birthDate`等附加属性，继承了`Thing`的所有属性如`name`。`Thing`有其他子类，如`Organization`，有自己的属性，如`logo`。

在第一期**中，我们最终揭示了一个递归的类型脚本继承层次结构，我们可以用它来模拟 Schema.org 类继承的全部复杂性。**

 **[![Recursive Hierarchy](img/2ab9aff17ca65aa1207a4818cc2b2f67.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jxgst6Oq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/blog.eyas.sh/wp-content/uploads/2019/05/type-system-recursive.png)

## 2。【Schema.org】打字稿中的枚举

当试图对枚举进行建模时，我们查看了来自 Schema.org 网站的大量示例，发现[绝对 IRI](https://www.w3.org/TR/json-ld/#iris)或`@context`相对 IRI 被期望对枚举的*值*进行建模。但是我们也发现枚举可以是任意的节点，并参与类的层次结构。

[![Example of an Enum Hierarchy](img/adbbac31ffc3f41f51015fb6c54ae8ff.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dSAcZWC1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/blog.eyas.sh/wp-content/uploads/2019/05/audience-schema-1.png)

## 3。打字稿中的 Schema.org 数据类型

Schema.org 数据类型层次结构比 TypeScript 的类型系统所能容纳的要丰富得多。在[第三期](https://blog.eyas.sh/2019/07/schema-org-datatype-in-typescript-structural-typing-doesnt-cut-it/)中，我们弄清楚了我们可以做哪些取舍。

## 4。[类属性和特殊情况](https://blog.eyas.sh/2019/07/schema-org-classes-in-typescript-properties-and-special-cases/)

属性——实际上存在于 JSON 节点中的所有*东西*——比我们想象的要复杂得多:它们都是可选的，它们都是重复的，它们可以相互取代，然后可以相互继承。

# 最终结果

最终结果是 **schema-dts** 本身。我们可以创建表达 Schema.org 大部分内容的编程式脚本定义。例如，Schema.org 的顶级`Thing`类型可以表示为:

```
type ThingBase = {
    /** An additional type for the item, typically used for adding more specific types from external vocabularies in microdata syntax. This is a relationship between something and a class that the thing is in. In RDFa syntax, it is better to use the native RDFa syntax - the 'typeof' attribute - for multiple types. Schema.org tools may have only weaker understanding of extra types, in particular those defined externally. */
    "additionalType"?: URL | URL[];
    /** An alias for the item. */
    "alternateName"?: Text | Text[];
    /** A description of the item. */
    "description"?: Text | Text[];
    /** A sub property of description. A short description of the item used to disambiguate from other, similar items. Information from other properties (in particular, name) may be necessary for the description to be useful for disambiguation. */
    "disambiguatingDescription"?: Text | Text[];
    /** The identifier property represents any kind of identifier for any kind of {@link http://schema.org/Thing Thing}, such as ISBNs, GTIN codes, UUIDs etc. Schema.org provides dedicated properties for representing many of these, either as textual strings or as URL (URI) links. See {@link /docs/datamodel.html#identifierBg background notes} for more details. */
    "identifier"?: (PropertyValue | Text | URL) | (PropertyValue | Text | URL)[];
    /** An image of the item. This can be a {@link http://schema.org/URL URL} or a fully described {@link http://schema.org/ImageObject ImageObject}. */
    "image"?: (ImageObject | URL) | (ImageObject | URL)[];
    /** Indicates a page (or other CreativeWork) for which this thing is the main entity being described. See {@link /docs/datamodel.html#mainEntityBackground background notes} for details. */
    "mainEntityOfPage"?: (CreativeWork | URL) | (CreativeWork | URL)[];
    /** The name of the item. */
    "name"?: Text | Text[];
    /** Indicates a potential Action, which describes an idealized action in which this thing would play an 'object' role. */
    "potentialAction"?: Action | Action[];
    /** URL of a reference Web page that unambiguously indicates the item's identity. E.g. the URL of the item's Wikipedia page, Wikidata entry, or official website. */
    "sameAs"?: URL | URL[];
    /** A CreativeWork or Event about this Thing.. */
    "subjectOf"?: (CreativeWork | Event) | (CreativeWork | Event)[];
    /** URL of the item. */
    "url"?: URL | URL[];
};
/** The most generic type of item. */
export type Thing = ({
    "@type": "Thing";
} & ThingBase) | (Action | CreativeWork | Event | Intangible | MedicalEntity | Organization | Person | Place | Product); 
```

在查看整个系列**