# 当您在 Eclipse 的代码完成中得到非预期的结果时，这可能是子串代码完成的副作用。

> 原文：<https://dev.to/riversun/when-you-get-unintended-results-in-eclipse-s-code-completion-that-might-be-the-side-effect-of-substring-code-completion-191f>

# 概述

*   当您想要完成一个类的方法名时，有时 eclipse 会完成一个非预期的方法名。
*   我想解释一下在这种情况下的措施。
*   这可能是在 [Eclipse NEON](https://www.eclipse.org/eclipse/news/4.6/jdt.php) 中添加的新特性“Substring code completion”的副作用

## 环境

*   eclipse IDE 2019-06

# “意外完成”如下。先看看。

输入“数组”后，输入一个点(。)开始代码完成。
接下来，如果输入“tostring”并按回车键，“deepToString”被选为问题。

[![substring_miss_before_.gif](img/21736953bf0f8c2c5dbf1cc71215858e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uuAzuf5Y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/170905/4a73e605-6104-2073-4648-e0e201c9ec63.gif)

## “子串代码补全”是个有用的功能，你懂的。

即使只键入“string ”,包含“string”作为方法名一部分的方法也将被建议作为候选方法。

[![substring_ok.gif](img/d79bc0fdf79cc8dfbf7e40510a08985b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--r2DKZ-g0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/170905/731b8d1d-2513-0898-18dd-94ea309b8b51.gif)

# 解决方案 1:关闭子串补全

选择**窗口>首选项> Java >编辑器>内容辅助**，则可以找到**显示子串匹配**设置 [![image.png](img/22feaaa59f02eb3ae4215b89d163376c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZfPuaEuJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/170905/ec003ceb-52de-271a-9cbd-83873e730029.png) 。然后关机 [![image.png](img/586eec5726f96cacbd5aab50ce6db980.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--p3FXKzPj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/170905/9a19cb9f-b238-e47a-79b4-e62be45a958b.png)

[![image.png](img/50afc1f32e306fcdf6f9d9f9534aa306.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HQZhv7dg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/170905/2902f2b0-17a6-ee7b-c77d-8216768ec1d8.png)

## 尝试使用“子串补全关闭”

感觉挺好的。
无意的“deepToString”不再补。

[![off_substring.gif](img/0560191e46f525e565e6af6abf5f6858.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PX-BuTM0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/170905/67d8801b-b59e-72d0-54e7-e1d0905f5e4d.gif)

## 但是，如果“子串补全”关闭，子串补全就无法自然完成。

我试着在“数组”后面输入“字符串”没有其他候选人了。

[![off_substring_miss.gif](img/60ce52d35f5424832bb45d0478e13a1a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--B3AOhqng--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/170905/b52be34e-0ade-edab-bc01-224daa9d054f.gif)

# 解-2

像以前一样重新打开设置窗口**窗口>首选项> Java >编辑器>内容辅助**并打开 [![image.png](img/428d01c59f15c01604c186267b4276be.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AItY_5kD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/170905/df96a4ec-3fbb-3a5d-4e11-47f5a1e2722a.png) 上的“子串完成”

接下来，点击“内容辅助”上的[+]“加号”标记，然后展开设置，选择“高级”。

[![image.png](img/692964fbf51e16593da657287692568d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tQZ1UbyJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/170905/f1919d60-d644-9c43-b327-b0974630dedd.png)

检查 **Java 提议**如下。

[![image.png](img/ab027df1179aec5552df301d5a7badd0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qsYlxRH_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/170905/2fc4eb7a-8d0c-aef9-e783-d835f4ada068.png)

## 试代码补全

看看这个。

它似乎是按照预定的动作移动的

[![proposals_fullmatch.gif](img/f56f1c93fefe7c1924ff14a108fa6344.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--b31g3jCE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/170905/85bd1c14-50d1-67d3-d92a-6c77bff26eae.gif)

## 用子串匹配尝试代码补全

子字符串完成也工作正常。

[![proposals_partmatch.gif](img/94203c02c5fd160bb367077585617bb5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TU-6kEn7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/170905/85f588ca-7683-2eb0-6a3c-1921d4dc1157.gif)

解决方案 2 似乎更符合预期。

# 印象

至于代码补全，我认为，“Intellij IDEA”(或者 Android Studio)要高级得多，对应的是“子串代码补全”，而且好像是预先调好的，没有这样的副作用。

(直到在 Eclipse 中遇到才注意到，但是使用感受的调优所改善的细微差异影响了生产力和质量等。)