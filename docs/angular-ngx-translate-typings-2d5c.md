# Angular + @ngx-translate +打字

> 原文：<https://dev.to/angular/angular-ngx-translate-typings-2d5c>

# 简介

当你决定在一个项目中使用 Angular 的时候，就意味着你在搭建一个大型的 SPA(否则你就要想别的框架了)。大型项目通常面向不同国家的用户，并通过应用程序的翻译来实施。

在我看来，属于 Angular 生态系统的应用中最好实现翻译的库是 [@ngx-translate](http://www.ngx-translate.com/) 。我从一开始就使用这个库，因为它简单、强大而且非常有用。

然而，我发现这个库最大的缺点是翻译基于一个键值翻译对象，在这个对象中，要识别你想要翻译的键，你必须指定一个字符串。使用字符串的事实导致我们失去了对想要翻译的变量的所有类型控制。

## **由此产生的问题有哪些？**

1.  我们无法知道一把钥匙是否丢失、不完整或有错别字。

2.  我们没有自动完成功能，尽管有可能很复杂的嵌套对象。

3.  你不能在所有语言之间自动改变一个键的名字(重构)，我们必须一个接一个地修改每个语言文件。

在本帖中，我们将构建一个小服务，它将允许我们在软件开发中大大改进@ngx-translate 的使用。

在开始之前，你可以在下一个 gif 中看到我们将要构建的最终结果:

[![](img/e3ea11a5e53b2ab75f75c558186497a0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KLx7ybAv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/3646/1%2AznWb53BaH13Y4b5x6HGaSA.gif)

## @ ngx-translate 的安装和配置

首先，按照作者在 GitHub 页面上的建议，以常规方式配置@ngx-translate。

[![](img/979f82f056157f43057285ff5d043ca3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mY_5guyv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AVJceByJuV_FgCMhapVekKQ.png)

首先，您需要安装 npm 模块:

[![](img/ca561046ecf2aa6a901039e1ca13b703.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--x6jLcD3x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AGwxTJCk9w772YeAdb_TjJA.png)

您必须在应用程序的根 NgModule 中导入 TranslateModule.forRoot()。

forRoot 静态方法是一种同时提供和配置服务的约定。确保你只在你的应用程序的根模块中调用这个方法，大部分时间是调用 AppModule。此方法允许您通过指定加载程序、解析器和/或缺失的翻译处理程序来配置 TranslateModule。

[![](img/46af0587843fdae4d5e726156c5a652b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vxGSet_O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2604/1%2ApaQQVWUNlLQYTwrSq7AF8g.png)

默认情况下，没有可用的加载程序。您可以编写自己的加载程序，或者导入现有的加载程序。例如，您可以使用 TranslateHttpLoader，它将使用 HttpClient 从文件中加载翻译。

要使用它，您需要从@ngx-translate 安装 http-loader 包:

[![](img/9a5c15f5c764c62c72c5a76fd9ca2b0d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qgAacR3l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2232/1%2ALHL_nc5H0FZcfpyjnUDcNA.png)

一旦导入了 TranslateModule，就可以将您的翻译放入一个 json 文件中，该文件将使用 TranslateHttpLoader 导入。下面的翻译应该存储在 eng.json 和 span.json 中。

[![](img/a8440558cff4a991c40198666c875f6a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--x1PKtK4Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2672/1%2AwdePE_TZ_Fkk8fE7waBztg.png)

[![](img/08cb5029135b3b4ae9804290544a096d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rQGju6a6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2840/1%2AmoutfVxBOs46FJ2ir_hDVQ.png)

您可以使用 TranslateService、TranslatePipe 或 TranslateDirective 来获取翻译值。

[![](img/6ad3083eec4a03ae167dc174b7a6d0f7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1G2vZ2i1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3312/1%2AOy5l_6CdHI1j-Co4cUcssA.png)

在组件中，如下定义 param:

[![](img/16ce21053a9b0278402e1f8c19aefa91.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QnxOuPw_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2704/1%2AK02KeYC9IoiWgioSU3b0ew.png)

# 键入@ngx-translate

我们需要做的第一件事是创建一个名为 translations 的新服务，它将解析指定的翻译键所在的路径。

当然，代码可以在以后重构，但是这篇文章的目的是说明如何输入@ngx-translate。

首先，已经定义了 **GenericClass** 函数，该函数用于向从其扩展的任何类提供属性:

[![](img/66d64ec7b30118b6ca6587608499b4cf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TJX_8S3H--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2608/1%2AXRwGPHHrasoS0yoooqE_sQ.png)

第二步是创建从 GenericClass 扩展的服务，其中指定属于 eng.ts 文件的属性将是 Translations 类的属性。

[![](img/baa5cc35548d76d2382b08eb7b70d029.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--y0rVCjJN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2808/1%2AX2GQP4LIfSRxdMkwIWGZOA.png)

下一步是构建 transformObjectToPath 函数，该函数负责使用。ts 文件，其中的值是完整的路径，以便@ngx-translate 接收它所期望的字符串。

[![](img/19f3b8aac4580e1412e1e9e610ffd517.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--z5Ul4zDU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2976/1%2AQgfHf-SGWkwjJs_Xbrfy7Q.png)

在执行此方法的末尾，该类将包含以下复合对象:

[![](img/1e4f7bde80daa30f3ce4ac91b1a14e95.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2vz4Yg-Y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2A_P3hw9h6xJxIgHMCsZFNEg.png)

最后，与服务相关的代码如下:

[![](img/4ad908b4a082e68c914b7b92399081dd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_OVtreqO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2976/1%2AjqkGtUrFd7G9kWZIS9vG0A.png)

带有翻译的文件是。TS 文件，而不是。json 文件，所以它们如下:

[![](img/f712e3e6b8d577f80f4ad915be600a39.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QgJBJNbT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2604/1%2AeckgvK1KSgi8udnx8kYeGw.png)

[![](img/dad58e00a2264781787d46a8fafdd383.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Y8xEi9Vx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2776/1%2AGbq0MoxwzDw-eLHTvNxoxw.png)

代码中必须做的另一个修改是为@ngx-translate 定义我们自己的加载器，因为。json 文件不是通过 httpClient 服务下载的，而是通过 charger 下载的，charger 将被创建来执行 TS 文件的加载，如下所示:

[![](img/b81dfcd211ebf1f6775bad75e1f0a7bf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sE06vEhH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3280/1%2AF2heiuzUIn-gUjsXzPGQOg.png)

这个加载器被称为 WebpackTranslateLoader，因为 Webpack 负责分析用关键字 import(...)并将它们打包成独立的源，以便动态地执行它们的请求。因此，在请求文件时，请求被发送到文件/assets/i18n/language.js。

[![](img/4816f57e26e15947c04f2585d3f714c8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Yg11iotO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2604/1%2AGuVi3ioQlhWsPudJGjH2Jw.png)

最后也是最令人愉快的一步是使用@ngx-translate，在您想要注入多语言文本的组件中键入内容。有必要注入翻译服务，该服务必须是公共的，以便能够与模板进行交互。

[![](img/4d68d4a62050d797dd0996909eef76d4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XOlruR-r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2568/1%2ATik_DNDnc5TZi1IfENUstQ.png)

最后，魔法可以在可用的东西中看到:

1.  自动完成。

[![](img/44802bc64d6319483e0c11231f00e5e5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lyfoS2u8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3840/0%2AlMuUe4fOZ0Xo-EZQ.png)

1.  丢失钥匙的检测。

[![](img/084364bbdd3c75a9dfa82e0cc10ba559.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UfIhfqav--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2Ahbcycq4UDTJfDwFAvML9FQ.png)

另一个有趣的可以锦上添花的地方是能够轻松地重构所有的语言文件。

在这种情况下，我们可以应用两种技术:

1.  **强打字**。如果语言文件中没有关键字，则发出警告。

[![](img/54b60b7ffb9fa6267d3572891ea43902.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eESuAC6W--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2232/1%2Ajq1WUZbfqULeuB1d7c5UBQ.png)

**2。打字**。让您键入内容，但如果缺少某个键，则不会发出警告的强制转换

[![](img/7695b0b3b80779e728fce5d9d42fced3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--to3Pwb74--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2A-RZ6Q93EtxUOWcn6x1EXfQ.png)

不可思议的最终结果是下面这张 GIF 中显示的结果。

[![](img/e3ea11a5e53b2ab75f75c558186497a0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KLx7ybAv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/3646/1%2AznWb53BaH13Y4b5x6HGaSA.gif)

该岗位的 *GitHub 分支*为 https://github.com/Caballerog/angular-ngx-translate-typing

最初发表于[https://carloscaballero.io/angular-ngx-translate-typings](https://carloscaballero.io/angular-ngx-translate-typings)