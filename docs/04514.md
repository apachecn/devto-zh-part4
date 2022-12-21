# 故障排除 npm 代理问题第 1 部分

> 原文：<https://dev.to/smithphil/troublshooting-npm-proxy-issues-part-1-8l0>

npm 包管理器是 JavaScript 开发人员的必备工具，但有时，在公司网络上意味着您必须通过代理来访问包。这是第一篇致力于解决 npm 代理问题的文章。

例如，如果您在我们的家庭网络上运行`npm install --save-dev grunt`, grunt 将按预期安装到您的项目中。如果你在一个有代理服务器的网络上运行，你可能会得到一个类似
的错误

```
npm ERR! code ETIMEDOUT
npm ERR! errno ETIMEDOUT
npm ERR! network request to https://registry.npmjs.org/grunt failed ... 
```

然后，错误文本会继续提示，如果您使用代理，您必须设置“代理”配置。这意味着您必须告诉 npm 代理在您网络上的地址。

实际上您必须设置两个配置设置:proxy 和 https-proxy，https-proxy 是 TLS 的地址。假设代理地址是[http://proxy.example.com:8080](http://proxy.example.com:8080)，那么只需输入

`npm config set proxy http://proxy.example.com:8080`

和

`npm config set https-proxy http://proxy.example.com:8080`

此时，npm 应该开始工作，否则可能会出现 403 错误。处理 403 错误将是第 2 部分的主题。

### 附加的东西

可以使用以下命令显示配置设置。使用`npm config ls`显示用户配置设置列表，所有配置设置都显示有`npm config ls -l`。

这第二个命令也会显示你的位置。npmrc 文件，这是您的用户配置文件。你可以直接输入你的配置设置到那个文件中并保存它，而不是使用 cli 命令，如果你也想的话，我更喜欢 cli。这里有更多关于 [npm 配置](https://docs.npmjs.com/cli/config)的细节。