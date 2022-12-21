# 解耦代码和秘密

> 原文：<https://dev.to/a0viedo/decoupling-code-and-secrets-1562>

前几天，我和一位同事讨论从[金库](https://www.vaultproject.io/)切换到[库本内特的秘密](https://kubernetes.io/docs/concepts/configuration/secret/)的问题。令我惊讶的是，我们的应用程序代码中有很多**依赖于我们的 Vault 实现**。我们的迁移需要为我们维护的每个服务打开一个 PR。在努力不做鬼脸的同时，我开始思考如何从一开始就避免这种情况。我想到了两个选择，我将在这里描述。

# 溶液 A

另一种方法是将处理 Vault 细节的逻辑打包成一个库。因此，一旦迁移到 K8s secrets，我们可以改变库的代码一次，这将影响使用它的服务。但是这不是一个跨语言的解决方案，也就是说，你必须为 Go 维护一个库，为 Node.js 维护另一个库。

# 溶液 B

另一个解决方案是将秘密作为环境变量注入到应用程序进程中。这种方法将您的应用程序从您的秘密管理方法中分离出来:对于您的所有应用程序，更改只发生一次。由于它比解决方案 A 具有更大的灵活性和可移植性，所以从长远来看，这种解决方案是更好的选择。该解决方案还遵循配置应用的 [12 因素应用](https://12factor.net/config)方法。

# 总之

处理应用程序的秘密是一个复杂的主题。即使您使用的解决方案可以解决我在这里提到的问题——与语言无关，并将其配置为映像/实例供应的一部分——您也不能免除应用程序逻辑日志记录的秘密。你将花更多的时间去思考、设计和实现分离的组件。这也将使您的架构更容易维护，更容易应用更改。

### 进一步阅读

如果你想详细阅读《秘密管理》,我选择了一些资源作为跟进:

*   [秘密管理指南](https://blog.cryptomove.com/secrets-management-guide-approaches-open-source-tools-commercial-products-challenges-db560fd0584d)
*   [管理密码和应用程序秘密:常见反模式](https://blog.envkey.com/managing-passwords-and-secrets-common-anti-patterns-2d5d2ab8e8ca)
*   [秘密管理架构:在安全性和复杂性之间找到平衡](https://medium.com/slalom-technology/secret-management-architectures-finding-the-balance-between-security-and-complexity-9e56f2078e54)

非常感谢[鲁本·布里奇沃特](http://twitter.com/BridgeAR)、[本](http://twitter.com/BenedekGagyi)和[乌吉瓦尔·夏尔马](http://twitter.com/ryzokuken)审阅了这篇博文的初稿。