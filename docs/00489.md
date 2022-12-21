# 在 Rails 测试中阻止对 AWS 的调用

> 原文：<https://dev.to/prathamesh/stubbing-calls-to-aws-in-rails-tests-2bgh>

在我的 Rails 应用程序中，我使用`aws-sdk` gem 来处理上传到 S3 的文件。我需要添加帐户徽标上传和删除功能的测试。我用回形针宝石做文件上传。

通常，应用程序在测试环境中使用文件存储来避免碰到 AWS API，但是在这个应用程序中，我被限制在测试环境中不使用文件存储。

很明显，我不想在测试中碰到 AWS API。我利用`aws-sdk` gem 提供的技术想出了一个解决方案。

只需删除`test_helper`或`rails_helper`中的下一行代码。

```
# Stub all calls to AWS
  config.before do
    Aws.config.update(stub_responses: true)
  end 
```

Enter fullscreen mode Exit fullscreen mode

你很棒。所有对 AWS 的 API 调用现在都被存根化了！

测试愉快。