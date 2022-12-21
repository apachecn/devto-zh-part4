# Android 主题再探:第 2 部分-排版

> 原文：<https://dev.to/pabiforbes/theming-in-android-revisited-part-2-typography-13h3>

给你的 Android 应用主题化不仅仅是选择一个配色方案，这是我犯的一个错误。主题是由其他因素组成的，比如排版。

你可能会认为字体设计只是选择一种字体，然后把它放到应用程序中，对吗？这是我犯的第二个错误😊。

# 什么是排版？

印刷术可以被定义为排列字体的艺术和技术。虽然这是一个我们通常依赖设计师来处理的领域，但我相信作为开发者，我们应该对它如何在幕后工作有所了解。

材料设计有一个非常酷的排版指南，可以在[这里](https://material.io/design/typography/understanding-typography.html#)找到。

## 样式文本

使用材质设计类型比例来设计应用程序中的文本有助于在整个应用程序中创建统一的外观。它提供了以下应用于文本的样式:

### 标题

标题样式的文本在屏幕上显示为最大。

#### 属性:

*   textAppearanceHeadline1
*   textAppearanceHeadline2
*   textAppearanceHeadline3
*   textAppearanceHeadline4
*   textAppearanceHeadline5
*   textAppearanceHeadline6

### 字幕

副标题样式的文本看起来比标题文本小。

#### 属性:

*   文本外观字幕 1
*   文本外观副标题 2

### 正文

正文通常用于长格式的写作，如文章。

#### 属性:

*   textAppearanceBody1
*   textAppearanceBody2

### 标题和上划线

这些字体最小。

#### 属性:

*   textAppearanceCaption 和 textAppearanceOverline。

### 按钮

用于设置按钮上显示的文本的样式。

#### 属性:

*   textAppearanceButton

# 材料设计型秤在行动

您可以在应用程序中使用字体缩放，例如 Headline，如下所示: