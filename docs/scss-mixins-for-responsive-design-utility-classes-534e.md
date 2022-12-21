# SCSS 混合响应设计，实用类

> 原文：<https://dev.to/prashantandani/scss-mixins-for-responsive-design-utility-classes-534e>

**问题:**

当我开始任何新的 web 应用程序时，我会担心响应式设计。我如何处理不同屏幕尺寸的不同视图？总是习惯于希望有自举类型的行/列间距，而没有自举。总想也有间距(填充、边距)的实用类。

容易记住的类名可以让我的问题消失。
SCSS Mixins 对于响应式设计来说，以上指定的实用类问题想必是几乎所有的 web 应用开发者都面临过的。如果你正在使用一些 CSS 框架，他们会为你提供不同的解决方案。但是，假设你有 SCSS mixins，它用大约 50 行 CSS 代码生成这些类并解决你的问题，而不是导入一个完整的框架(例如 Bootstrap)。

别担心，我有办法解决你的烦恼。如果你没有使用 SCSS，那么使用在线转换器将 SCSS 代码转换成 CSS。

请导入此文件

[https://github . com/malothnaresh/CSS-utilities/blob/master/common . scss](https://github.com/malothnaresh/css-utilities/blob/master/common.scss)
用法:

间距实用程序类别:

该文件中的第一次混合创建了大多数所需的间距实用程序类。使用的单位是`rem`。而你指定的每一个计数都是`0.25rem * #{count}`。

例如:

p-0:向四周填充`0rem`；
m-0:所有边的边距`0rem`；
p-1:向四周填充`0.25rem`；
m-2:所有边的边距`0.50rem`；
p-3:向四周填充`0.75rem`；
m-4:向四周边距`1rem`；
pl-5:padding left 1.25 rem；
Mr-6:MarginRight 1.50 rem；
pt-7:padding top 1.75 rem；
m b-8:margin bottom 2 rem；
所有的类名都是:

填充所有边:[p-0，p-1，…p–20]
边距所有边:[m-0，m-1，…m-20]
填充左边:[pl-0，pl-1，…pl-20]
填充右边:[pr-0，pr-1，…pr–20]
填充上边:[pt-0，pt-1，…pt–20]
填充下边[pb-0，pb-1，…Pb–20]
左边距:[ml
行/列分隔线:

该文件中的第二个 mixin 将一行分成 12 列(Bootstrap 的行/列设计模式)。每一行都是具有 12 列的“显示:伸缩”。

例如:

该内容在大屏幕上为 3 列，在中等屏幕上为 6 列，在小屏幕和极小屏幕上为 12 列

根据屏幕尺寸的所有类别名称:

极小屏幕(最大宽度:480 像素):

col-xs-1、col-xs-2、col-xs-3、col-xs-4、col-xs-5、col-xs-6、col-xs-7、col-xs-8、col-xs-9、col-xs-10、col-xs-11、col-xs-12。

小屏幕(最大宽度:768 像素):

col-sm-1，col-sm-2，col-sm-3，col-sm-4，col-sm-5，col-sm-6，col-sm-7，col-sm-8，col-sm-9，col-sm-10，col-sm-11，col-sm-12。

中等屏幕(最大宽度:1125 像素):

col-md-1、col-md-2、col-md-3、col-md-4、col-md-5、col-md-6、col-md-7、col-md-8、col-md-9、col-md-10、col-md-11、col-md-12。

大屏幕(桌面/监视器屏幕):

列-lg-1，列-lg-2，列-lg-3，列-lg-4，列-lg-5，列-lg-6，列-lg-7，列-lg-8，列-lg-9，列-lg-10，列-lg-11，列-lg-12。

希望这能引导你的风格…

Medium Doc 链接:[https://medium . com/@ cryptoipl/scss-mixins-for-responsive-design-utilities-classes-4194 a 25 e7a 99](https://medium.com/@cryptoipl/scss-mixins-for-responsive-design-utilities-classes-4194a25e7a99)

供稿:Maloth Naresh