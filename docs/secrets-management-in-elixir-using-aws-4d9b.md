# 使用 AWS 在 Elixir 中进行秘密管理

> 原文：<https://dev.to/caredox/secrets-management-in-elixir-using-aws-4d9b>

当我们 Caredox 决定开始用 Elixir 编写服务时，构建一个安全存储和访问机密的解决方案是我们想要解决的第一个行动项目之一。我们的基础设施一直托管在 AWS 上，因此我们继续与他们的服务集成以存储我们的秘密是有意义的。在考虑了 AWS 提供的一些加密选项后，我们决定继续使用参数存储:AWS 系统管理器(SSM)的一个组件。

Caredox 使用蒸馏器为我们的酏剂应用程序构建版本。Distillery 有一个叫做[配置提供者](https://hexdocs.pm/distillery/extensibility/config_providers.html)的概念，通过解析指定的文件来填充运行时环境。取决于秘密是如何存储的(JSON，TOML，YAML 等。)，每个存储机制都需要自己的配置提供程序。我们没有找到一个旨在与 SSM 集成的配置提供者，所以我们[编写了我们自己的](https://github.com/caredox/aws_ssm_provider)，它可以作为[十六进制包](https://hex.pm/packages/aws_ssm_provider)获得。我们的配置提供者为我们提供了一个相对简单的访问机密的过程。在启动我们的应用程序之前，我们从 AWS SSM 请求我们的秘密，并把它们写到一维 JSON 文件中。我们的配置提供程序读取这个平面 JSON 文件，并生成一个多维数据对象，应用程序的运行时可以使用该对象来访问敏感值或其他值。当在 SSM 中更新或添加值时，必须重启正在运行的 docker 容器以反映这些更改。

# 具体例子

当添加需要新配置值的功能时，我们首先将该值添加到 SSM。我们遵循 AWS 建议的准则，将键定义为斜杠(`/`)分隔的字符串，即。`/production/caredox/Redix/password`。第一段是环境(生产)，第二段是您的项目(caredox)，第三段是 OTP 应用程序的名称(Redix)，所有剩余的段是您希望存储在该 OTP 应用程序中的密钥(密码)。

```
aws ssm put-parameter --name '/production/caredox/Redix/password'\
 --type ‘SecureString’ --value 'YourSecretPassword' 
```

然后，我们将应用程序部署在 docker 容器中。我们的 Dockerfile 指定了一个 bash bootscript 的入口点，它从 SSM 请求我们的秘密，将它们写入一个文件，然后启动我们的 Elixir 应用程序。

```
aws --region us-east-1 ssm get-parameters-by-path --path "/production/YOUR_PROJECT/"\
--recursive --with-decryption --query "Parameters[]" > /etc/app_secrets.json
/path/to/your_project foreground 
```

现在我们的容器有了一个包含我们秘密的文件，配置提供者准备好读取这个文件了。

```
# rel/confix.exs

environment :prod do
  set(config_providers: [{AwsSsmProvider, ["/etc/app_secrets.json"]}])
end 
```

# 分享例子

有时，我们有多个应用程序依赖于一个配置的真实来源。这可能是每个环境都需要的(暂存与生产)，或者我们可能希望配置在整个系统中具有默认值，而不管环境如何。为了获取全局机密，bootscript 将需要额外调用 SSM。我们碰巧把它们写给了`/etc/global_secrets.json`

对于在特定环境中保持一致的值，我们使用神奇的字符串`host_app`向 SSM 配置提供者表明，每个应用程序负责管理如何设置配置。例如，按环境的全局 SSM 配置看起来像这样`/staging/global/host_app/company_internal_password`。

必须告诉配置提供者哪个原子将替换 SSM 键中的字符串`host_app`，在这种情况下，要替换的原子是`:myApp`。我们通过将`:myApp`作为第二个元素包含在发送给配置提供程序的 init 函数的列表中来实现这一点。

```
# rel/confix.exs

environment :prod do
  set(config_providers: [{AwsSsmProvider, ["/etc/global_secrets.json", :myApp]}])
end 
```

# 结论

我们的秘密现在安全地存在于 AWS SSM 中，并在开始运行后被写入每个 docker 容器中的一个临时文件中。集装箱本身位于一个安全的 VPC，所以我们没有松散的结束。