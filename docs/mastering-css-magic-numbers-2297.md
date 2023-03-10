# 掌握 CSS:神奇的数字

> 原文：<https://dev.to/adrianbdesigns/mastering-css-magic-numbers-2297>

我们都在代码中看到过幻数，不是吗？神奇的`padding-top: 2px;`和`top: 136px;`就像随机散布的泡泡糖，将纸牌屋连接在一起。代码应该易于阅读，自我解释，并有一个逻辑结构。

神奇的数字可以被视为非常良性的小项目，例子和个别案件，但他们真的可以让我们的工作困难时，增加新的功能，解决问题和维护网站。很快，您会发现自己添加了一些神奇的数字来解决由其他神奇数字引起的问题，但也可能会引起新的问题。

## 解决方案从设计开始

当一个项目有一个设计阶段，该阶段产生一个带有样式指南、组件、模块、页面等的设计文件。我们通常有关于颜色、排版、水平网格和媒体查询变量的所有信息，但这些只是可以在整个网站中重复出现的许多变量中的几个。水平和垂直间距([垂直节奏](/adrianbdesigns/mastering-css-vertical-rhythm-om9))，或者边框，或者阴影，容器宽度，图像长宽比，z 索引等等。

在设计阶段定义的变量越多，设计在这些变量中受到的约束越多，我们就越不依赖幻数，因为我们可以看到所有设计元素之间的联系。例如，定义一个节奏单位可以添加一个更好的垂直间距上下文。现在，如果我们看到标题在`96px`高度，如果我们知道我们的基线高度是`32px`，我们可以看到标题容器中的所有元素都遵循垂直节奏的[规则。这允许我们在一个风格引导变量(节奏单元)和一个复杂模块(头)之间建立一个更好的连接。](/adrianbdesigns/mastering-css-vertical-rhythm-om9)

## 纪律造就强大的开发者

不管是好是坏，没有什么能阻止我们在 CSS 中使用幻数。如果我们写一个值或包含该值的变量，代码将同样工作。任何 linter 或预处理器都不会抛出错误。开发人员应该学习最佳实践，超越职责范围，做出高质量的长期决策。当开始一个新项目时，概述和定义 CSS 变量是非常重要的。代码的质量、可读性和可维护性将取决于你如何定义这些变量:给它们起一个有意义的名字，恰当地将变量分组，定义变量之间明确的依赖关系，等等。

让我们用下面的代码来演示一下。请注意，在这个例子中，我使用了 PostCSS 和现代 CSS 语法。但是这种理念也适用于像 SASS 和 LESS 这样的 CSS 预处理程序。

```
:root {
    /* px definitions for easier calculations */
    /* These variables are only used for calculation, we ommit the px because calc cannot strip units */
    --typography__fontSize--px: 16;
    --typography__lineHeight--px: 27;

    --browser__fontSize--default: 16;

    /* Basic typography setup, establishing rhythm unit */
    /* These variables should be assigned to the CSS attributes */
    --typography__fontSize: calc(
        (var(--typography__fontSize--px) / var(--browser__fontSize--default)) * 1rem
    );
    --typography__lineHeight: calc(
        (var(--typography__lineHeight--px) / var(--typography__fontSize--px)) * 1rem
    );

    /* Calculating vertical rhythm variables */
    /* Can be located in separate file if using Preprocessors or PostCSS import */
    --spacing__vertical--1: var(--typography__lineHeight);
    --spacing__vertical--2: calc(2 * var(--spacing__vertical--1));
    --spacing__vertical--3: calc(3 * var(--spacing__vertical--1));

    /* Defining custom variables for modules */
    /* Can be located in separate file if using Preprocessors or PostCSS import */
    --header__height: var(--spacing__vertical--3);
} 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我们只使用了两个数字，其余的变量是如何与这两个数字相关联的。我已经用我自己的方式命名了变量，用类似 BEM 的结构`group__attributeName--variation`。变量之间的联系很清楚:基本节奏单位等于基线高度，其中`rem`值是使用字体大小和行高的`px`值计算的。同样，标题有一个固定的高度，等于节奏单位乘数 3，它是与其余节奏单位乘数(垂直间距)一起计算的。

以这种方式组织变量，我们可以很容易地增加代码的复杂性，避免重复的值或变量，避免一起使用幻数。但是...

## 有时，使用幻数是不可避免的

由于 CSS 的黑客性质和不同的浏览器支持，有时唯一的解决方案是使用幻数。即使在这种情况下，幻数也应该保存并记录在使用它的同一个文件的变量中。应用于单个元素的幻数不应该沿着全局变量定义。这样，任何开发人员都会理解变量的范围和作用，如果需要的话，将来可能会想要删除它。

```
/* element.css */

:root {
    /* Browser-specific bug, expected to be fixed in the future */
    --element__spacing--magic: -1px;
}

.element {
    margin-top: var(--element__spacing--magic);
} 
```

Enter fullscreen mode Exit fullscreen mode