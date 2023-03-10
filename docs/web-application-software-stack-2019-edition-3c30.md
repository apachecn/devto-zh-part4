# Web 应用程序无聊堆栈

> 原文：<https://dev.to/cybermischa/web-application-software-stack-2019-edition-3c30>

在 [JetBridge](https://jetbridge.com) 我们喜欢与客户一起开发我们引以为豪的软件应用程序，同时拓展我们的知识和专业领域。因为我们经常开始新的项目，所以我们已经标准化了一套和谐且富于表现力的工具、库和框架，以帮助我们快速推出新的应用程序，并以最少的重复交付尽可能多的价值。

我们的设置并不完美，也不是每个项目的终极堆栈，但它是我们多年来发展的，对我们来说非常好。我们将继续学习新的工具和技术，并改进我们的工作流程，因此请将此视为一个及时的快照。如果你在 2019 年 7 月没有读到这篇文章，那么我们可能至少修改了堆栈的某些部分。

### 方法学

我们的软件开发理论是:不要把事情过度复杂化。

实用主义和商业价值是压倒一切的关注点，而不是最新、最酷、最时髦的框架或技术。我们和任何极客一样喜欢玩新的酷东西，但我们不相信仅仅为了新奇或感觉不时髦而使用新东西。成熟度和支持应该是决定应用程序所基于的库或框架的因素，可维护性、社区、可用文档和支持，当然还有它为我们和我们的客户带来的实际价值。

有一种趋势，许多工程师不得不使软件比它需要的更复杂。当广泛可用且已知的工具已经可以完成工作时，使用非标准工具。试图把某人在黑客新闻上读到的某项巧妙的技术硬塞给它并不真正适合的东西。当已经存在可以扩展以执行期望任务的现有服务时，依赖额外的外部服务。当更多的抽象可以真正简化事情时，使用太低级的东西；或者当简单的系统级工具或语言可以更方便地完成事情时，使用太花哨和复杂的东西。

简单性是一种策略，当明智地使用时，可以极大地提高代码的可读性和可维护性，并导致易于管理的操作环境。

### 前端

在我写这篇文章的时候，我们使用的所有框架和库很可能已经被很酷的新的时髦 JS jams 所取代，你会嘲笑我们不时髦的选择。然而，这是我们今天行之有效的方法:

*   **React:** Vue 在 GitHub 上可能有更多的明星，但 React 仍然是行业标准，被脸书等公司积极使用和支持。使用 React hooks 编写应用程序真的感觉我们越来越接近函数式编程，增加了新的可组合性和代码重用水平，这是以前使用 HOCs 笨拙地实现的。
*   React 的 Material-UI 是一个工具包，它拥有你可能需要的几乎所有种类的小部件和实用工具，强大的主题和样式选项，非常平滑地集成 CSS-in-JS，开箱后看起来很坚固。它本质上是 Google 发布的 UI 范例的一个实现，所以在它的约束和可视化语言范围内工作会给你一个很好的基础。
*   **Create-React-App/React-scripts:**这真的做了你需要的一切，并用 sane 缺省值配置你的新 React 应用。你再也不需要摆弄 Webpack 或 HMR 了。我们已经[扩展了](https://github.com/jetbridge/create-react-app) CRA/r-s，推出了带有额外 ESlint 和更漂亮选项和故事书的新前端项目。
*   **[故事书](https://storybook.js.org/) :** 我们更喜欢使用模拟数据建立一个由独立实现的小型和大型组件组成的组件库，而不是总是在整个应用程序中编码和测试布局和设计。这使得 UI 开发人员在完成后端端点时不会被阻塞，有助于加强可重用和自包含组件的概念，并让我们可以轻松地预览各种界面状态。
*   **TypeScript:** 现在每个人都在使用 TypeScript，因为它很好，你也应该这样做。确实需要一些时间来适应和学习如何正确地使用 React 和 Redux，这需要少量的学习，但这是完全值得的。记住:你永远不需要使用`any`。当你认为你需要使用`any`时，你可能只需要添加一个类型参数(泛型)。
*   **ESLint:** ESlint 现在可以很好地处理打字稿了！不要忘记设置 `extends: ['plugin:@typescript-eslint/recommended', 'plugin:react/recommended', 'react-app']`
*   **更漂亮:**设置你的编辑器，当你点击保存时，它会更漂亮地运行你的代码。它不仅加强了一致的风格，而且也意味着你可以更轻松地格式化你的代码。打字更少，但格式更好。
*   Redux: Redux 很好听...我想是的。你确实需要一个中心位置来存储你的用户认证信息之类的东西，而 [redux-persist](https://github.com/rt2zz/redux-persist) 非常方便。本着让事情变得简单的精神，问问你自己你正在做的事情是否需要 redux。也许你可以，或者你可以用一个钩子或者状态来代替。当然，一开始你可能会想在 redux 中缓存一些 API 响应，但是如果你开始添加服务器端过滤、搜索或排序，那么在你的组件中作为一个简单的 API 请求会更好。
*   **异步/等待:**停止使用 Promise API！捕捉 UI 组件中的异常，您实际上可以向用户显示错误，而不是在 API 层中。
*   Axios :首选的 HTTP 客户端。我们使用 JWT 进行身份验证，并推荐我们的 [axios-jwt 拦截器模块](https://www.npmjs.com/package/axios-jwt)负责令牌存储、授权头和刷新。

我不相信这里有任何疯狂或不寻常的事情，这就是问题的关键。坚持标准，除非你有充分的理由不这样做。

### 后端

我们的后端服务始终围绕 [12 因素应用程序原则](https://12factor.net)设计，并且始终构建为云原生的，并且在适当的时候，[无服务器](https://read.acloud.guru/serverless-is-a-state-of-mind-717ef2088b42)。

大多数项目都涉及到设置典型的 REST API、与其他服务对话以及在 PostgreSQL 数据库上执行 CRUD。我们的首选堆栈是:

*   Python 3.7。Python 是干净的，可读的，在 PyPI 上有一个令人印象深刻的大规模社区模块库，活跃的核心开发，以及高级动态特性的良好平衡，而不会变得太迟钝或分散注意力。
*   键入注释并用`[mypy](http://mypy.readthedocs.io/http://mypy-lang.org/)`键入林挺。Python 确实有类型注释，但是它们非常有限，没有很好地集成，并且通常对于捕捉错误不是很有用。我希望情况会有所改善，因为与 TypeScript 或 Go 等语言相比，Python 中的许多错误必须在运行时发现。这在我看来是 Python 最大的缺点，但是我们用`mypy`做到了最好。
*   [Flask](https://palletsprojects.com/p/flask/) ，一个轻量级的 web 应用框架。Flask 非常适合构建 REST APIs，为您的应用程序提供了足够的结构来处理 WSGI、配置、数据库连接、可重用 API 处理程序、跟踪/调试(使用 [AWS X-Ray](https://aws.amazon.com/xray/) )、日志记录、异常处理、身份验证和灵活的 URL 路由。除了提供胶水将所有东西粘在一起形成一个连贯的应用程序，而不会强加太多的开销或样板文件，我们不太依赖 Flask。
*   [SQLAlchemy](https://docs.sqlalchemy.org) 用于声明性 ORM。具有处理 Postgres 方言特性的良好特性，例如`UPSERT`和`JSONB`。为模型和查询类组合 mixins 的能力非常强大，我们越来越多地将它用于像软删除这样的特性。[多态子类型](https://docs.sqlalchemy.org/en/13/orm/inheritance.html#single-table-inheritance)是最有趣的 SQLAlchemy 特性之一，允许您定义一个类型鉴别器列，并基于其值实例化适当的模型子类。
*   测试:[包装每个测试的子事务](https://pypi.org/project/pytest-flask-sqlalchemy/)， [pytest-factoryboy](https://pytest-factoryboy.readthedocs.io/en/latest/#model-fixture) ，用于从我们的模型类为 pytest 生成 fixtures，并为开发环境生成模拟数据。切尔莱西。 [Pytest 夹具](https://docs.pytest.org/en/latest/fixture.html)。[烧瓶测试客户端](https://flask.palletsprojects.com/en/1.1.x/testing/)。
*   带有[棉花糖](https://marshmallow.readthedocs.io/en/3.0/)的 Flask-REST-API 有助于用最少的样板文件简洁地定义 REST 端点、序列化和验证，在适当的时候大量使用装饰器来获得声明性的感觉。另外，它还生成 OpenAPI 规范文档，并附带 Swagger-UI 来自动提供每个 API 端点及其参数和响应形状的文档，无需任何额外的工作。
*   我们目前正在开发 [Flask-CRUD](https://github.com/jetbridge/flask-crud) 以进一步减少 CRUD APIs 常见情况下的样板文件，并强制执行严格的数据模型访问控制检查。

在需要它的项目中，我们可以使用 Heroku 或 EC2 进行托管，但我们最近的所有项目都足够简单，可以构建为无服务器应用程序。在本文中，您可以更详细地了解我们的设置及其带来的好处[。](https://spiegelmock.com/2018/11/21/rapid-api-serverless-development/)

我们已经构建了一个初学者工具包，它将我们所有的后端部分结合在一个强大的模板中，以引导新的无服务器 Flask 项目，名为 [sls-flask](https://github.com/jetbridge/sls-flask/) 。如果您正在考虑用 Python 构建一个数据库支持的 REST API，那就试试吧！您可以在一个小套件中获得强大的功能和灵活性。它没有任何特别或奇特的东西，但我们相信它提供的基础构成了一个极其精简和现代的开发工具包。

我们所有的工具和模板都是开源的，我们经常向我们使用的模块提供错误报告和修复。我们鼓励您试用我们的堆栈，或者让我们知道您正在使用什么，如果您对您正在做的事情感到满意的话。[分享享受](https://www.urbandictionary.com/define.php?term=share%20and%20enjoy)！