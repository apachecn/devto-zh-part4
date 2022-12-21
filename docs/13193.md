# 部署显示错误“缺少加密密钥”的 rails。

> 原文：<https://dev.to/ehsanatwork/problem-in-rails-credentials-in-production-2gc9>

您好，
我正在生产中使用 rails 凭证。比如下图。但是当我试图把它上传到 digitalocean 时，它显示错误

`Rake aborted!
01 NoMethodError: undefined method `[]' for nil:NilClass`

这段代码的问题在哪里？

这是我的凭证结构，我用下面的代码调用这些凭证。

`:user_name => Rails.application.credentials.sendgrid[:user_name],
:password => Rails.application.credentials.sendgrid[:password],`

我该如何解决这个问题？