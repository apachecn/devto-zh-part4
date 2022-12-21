# 使用 RxJS 测试工具进行大理石测试

> 原文：<https://dev.to/kreuzerk/marble-testing-with-rxjs-testing-utils-25f7>

#### 大理石检测不需要第三方库

[![](img/42726cc694455e9a28e21b280c78b7a2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--i805qX8G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Abjcmm2W0TSLjwfJPbcKaIA.png)

一旦你开始了掌握可观测量的道路，你很有可能已经在路上遇到了大理石图。

marble 语法是一种非常直观的表示流的语法，通常用于可视化可观察的转换。它不仅是一种文档形式，还可以用来测试我们的可观测量。

通过咨询各种开发人员，我发现团队经常利用第三方库来应用 marble 测试。使用第三方库是一种有效的方法，没有任何问题。但是！

[* *不使用第三方库居然可以“弹珠测试”！**](https://medium.com/@kevinkreuzer/marble-testing-with-rxjs-testing-utils-3ae36ac3346a) 🤩