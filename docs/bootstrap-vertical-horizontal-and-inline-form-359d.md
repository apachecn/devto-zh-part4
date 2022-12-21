# 引导垂直、水平和内联形式

> 原文：<https://dev.to/speedysense/bootstrap-vertical-horizontal-and-inline-form-359d>

本文将讨论自举表单布局垂直表单、水平表单和内嵌表单。表单是几乎每个网站收集用户输入数据的基本部分。在[这篇文章](https://speedysense.com/bootstrap-vertical-horizontal-inline-forms-example/)中，我们将学习如何使用 Bootstrap CSS 框架创建表单。我们将介绍引导 3 和引导 4 表单布局。

**Bootstrap 4 表单布局**
Bootstrap 提供两种类型的表单布局。此外，您可以使用 Bootstrap 4 辅助类创建水平布局:

1.  [堆叠表单(全幅表单)](https://speedysense.com/bootstrap-vertical-horizontal-inline-forms-example/#bt4_stacked_form) -在包装元素中使用表单组类，每个表单元素添加。创建堆叠结构的窗体控件类。
2.  为了创建内嵌表单，向元素中添加 form-inline 类。此外，所有表单元素都与至少 576 像素宽的屏幕视窗左对齐。低于 576px 屏幕，会自动叠加。
3.  [水平自定义表单](https://speedysense.com/bootstrap-vertical-horizontal-inline-forms-example/#bt4_horizontal_form) -所有标签和表单控件都使用 col-md-*来进行两列水平布局。它将在窗体组和行类< div >容器之间换行。所有标签和输入字段在单行中左对齐。

**Bootstrap 3 表单布局**
Bootstrap 提供三种类型的表单布局:

1.  [垂直表单](https://speedysense.com/bootstrap-vertical-horizontal-inline-forms-example/#bt3_vertical_form)(这是默认的)——这是默认的引导表单布局，其中所有的输入字段和标签都是垂直对齐的。不需要在表单及其子元素上应用任何额外的引导类。让我们创建一个简单的垂直表单布局。
2.  [水平表单](https://speedysense.com/bootstrap-vertical-horizontal-inline-forms-example/#bt3_horizontal_form) -创建一个水平表单，所有的标签和输入字段在一行中左对齐。只需要在<表单>元素中添加一个表单水平类。而且所有的标签和表单控件都使用 col-md-*做两栏水平布局。快速检查横向表单布局。
3.  [内嵌表单](https://speedysense.com/bootstrap-vertical-horizontal-inline-forms-example/#bt3_inline_form) -所有表单元素与屏幕视图左对齐，至少 768 像素宽。您需要将额外的类 form-inline 添加到<表单>元素中。让我们看看 Bootstrap 3 内联表单布局。

**备注:**

1.  Bootstrap control-label 和 form-control 类分别用于设计标签和表单元素的样式。
2.  要控制文本框的大小，可以使用 input-lg 和 input-sm 类。