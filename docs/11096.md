# DDD/六角形架构技巧和诀窍:用 ComponentScan 将域绑定到 Spring 上下文

> 原文：<https://dev.to/julientopcu/ddd-hexagonal-architecture-tips-tricks-binding-the-domain-to-the-spring-context-with-componentscan-kj3>

[六边形架构](https://dev.to/julientopcu/hexagonal-architecture-decoupling-your-technical-code-from-your-business-logic-hexarch-2o89-temp-slug-2516502)告诉我们，领域内不应该存在任何框架，以避免技术上的意外复杂性，并在不重新开发部分业务逻辑的情况下，简化向新结构框架(或主要版本)的迁移。这意味着当你在使用 [Spring](https://spring.io/) 时，你不能依赖任何原型注释，比如你的域内的 [@Service](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/stereotype/Service.html) 或 [@Component](https://docs.spring.io/spring/docs/current/javadoc-api/index.html?org/springframework/stereotype/Component.html) 。

# 豆子宣言马拉松

因此，我们通常会在 Spring 配置中使用 bean factory 方法，并使用大量样板代码来实例化域服务、存储库和存根，因为我们认为不能将 [ComponentScan](https://www.baeldung.com/spring-component-scanning) 用于域对象。