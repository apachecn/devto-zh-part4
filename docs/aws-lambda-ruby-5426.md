# AWS Lambda & Ruby

> 原文：<https://dev.to/calebwatters/aws-lambda-ruby-5426>

在最近学习了如何使用 Rails 框架之后，我研究了部署该应用程序需要做些什么。Heroku 是这样做的一个便宜的选择，但是我有兴趣看看对于 ruby/rails 应用程序开发是否有更好的选择。考虑到 Rails 已经存在了多长时间，关于这个主题的信息显然是无穷无尽的。然而，在研究部署策略时，我发现了一些关于使用 AWS Lambda 进行部署的相对较新的 Ruby 框架的信息。

##### 这里是 AWS lambda 启动页面的一个片段

T2`AWS Lambda lets you run code without provisioning or managing servers. You pay only for the compute time you consume - there is no charge when your code is not running. With Lambda, you can run code for virtually any type of application or backend service - all with zero administration. Just upload your code and Lambda takes care of everything required to run and scale your code with high availability. You can set up your code to automatically trigger from other AWS services or call it directly from any web or mobile app.`

在 AWS Lambda 页面和 Jets 网站的 Ruby 上有很多关于 Lambdas 的其他重要信息。本质上，Jets 的功能类似于 Rails 的框架，但现在 AWS Lambda 支持 Ruby 函数，它允许您构建无服务器应用程序。关于如何构建和部署 Jets 应用程序，Medium 上有一篇很棒的博文。

[![Phil Nash](img/048093e478b428103011e90e114b3b36.png)](https://hackernoon.com/serverless-ruby-on-aws-lambda-with-the-jets-framework-989147f9d07b) [## 使用 Jets 框架的 AWS Lambda 上的无服务器 Ruby

### 菲尔纳什 8 分钟阅读

![Medium Logo](img/c01dc7f5c7558cbe9362be9fff1df297.png) hackernoon.com](https://hackernoon.com/serverless-ruby-on-aws-lambda-with-the-jets-framework-989147f9d07b)

作为这篇文章的姊妹篇，我计划写一个我自己的使用 Jets 的构建，以及我对它的可学性的想法。它的大部分格式看起来非常类似于 Ruby 的格式，但是当涉及到 Labmdas 时，它确实需要您有一定程度的理解。到目前为止，这些文件对我所做的研究非常有益。我还将附上另一篇关于在 Ruby 中创建 Lambda 函数的中型文章。

[![Mohamed Labouardy](img/3519b79106f22b96c3d3f08b4deaeb55.png)](https://hackernoon.com/build-a-ruby-based-lambda-function-44f3d006fef6) [## 构建一个基于 Ruby 的 Lambda 函数

### Mohamed Labouardy 3 分钟阅读

![Medium Logo](img/c01dc7f5c7558cbe9362be9fff1df297.png) hackernoon.com](https://hackernoon.com/build-a-ruby-based-lambda-function-44f3d006fef6)

本质上，Ruby 对编写 Lambdas 的支持和 Jets 框架的结合允许你单独用 Ruby 构建一个完整的应用程序。虽然我知道大多数人不会选择用 Ruby/编写的前端。雇员再培训局，从技术上讲，这是你可以选择的。考虑到我对 Ruby 和 Rails 的理解还算不错，这是一件很实际的事情。

##### 结论:

Jets 是一个相当新的框架，它允许开发者通过使用 AWS lambda 来创建“无服务器服务”, AWS lambda 现在支持 Ruby。

我计划继续记录我的学习之旅，通过自学如何用 Ruby 编写 lambdas，最终从头开始构建一个完整的 Jets 应用程序。

##### 参考文献:

[https://hacker noon . com/server less-ruby-on-AWS-lambda-with-the-jets-framework-989147 f9d 07 b](https://hackernoon.com/serverless-ruby-on-aws-lambda-with-the-jets-framework-989147f9d07b)
[https://hacker noon . com/build-a-ruby-based-lambda-function-44 F3 d 006 Fe F6](https://hackernoon.com/build-a-ruby-based-lambda-function-44f3d006fef6)
[https://aws.amazon.com/lambda/](https://aws.amazon.com/lambda/)
[https://rubyonjets.com/docs/](https://rubyonjets.com/docs/)