# 你会使用什么作为一个可排序的，全球唯一的 ID？

> 原文：<https://dev.to/rhymes/what-would-you-use-as-a-sortable-globally-unique-id-5akb>

目标是拥有一个具有以下两个固有属性的 ID:

*   全局唯一:如果两个不同的节点生成一个 ID，它们冲突的可能性几乎为零(因此没有自动递增的 ID)

*   可排序的:id，即使是由两个不同的节点生成的 id，也可以排序或部分排序，这使得对事件排序变得容易得多(因此没有 UUIDs)

你会用什么？