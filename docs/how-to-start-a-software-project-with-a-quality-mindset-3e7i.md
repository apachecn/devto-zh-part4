# 如何以质量的心态开始一个软件项目

> 原文：<https://dev.to/brpaz/how-to-start-a-software-project-with-a-quality-mindset-3e7i>

构建高质量的软件不是一件容易的事情，需要大量的实践和经验。

像编写单元测试或文档这样的重要活动仍然被许多人视为“额外的”，是开发人员在截止日期临近时削减的第一件事。

跳过这些活动可能会给你一种错觉，你在短期内更快，但最终，你将付出长期的代价，因为软件变得越来越复杂，越来越难理解和维护，技术债务越来越多。

bug 会更频繁的出现。由于缺乏自动化测试，人们将开始害怕对代码库进行大的修改和重构。

开发人员在如此混乱的代码库中工作会感到沮丧。产品所有者也会感到沮丧，因为构建和发布新功能需要越来越多的时间。

> 产品经理必须理解可维护性是产品交付的关键部分，因为它允许你继续交付特性。科技债务有复利。因此，你坚持为提供功能而削减的每一个角落都是对未来的借用。布莱恩·P·霍根

质量不是你以后可以轻易添加的东西。

你可以试试，但相信我，你等待的时间越长，让科技债务增加，就越难赶上。没有一个开发人员愿意花几个月的时间为她可能根本就没写过的旧代码编写单元测试。没有一个产品所有者会接受几个月不发布新特性而只是清理代码。

为了避免这些问题，并有一个更可持续的开发周期，这就是为什么您应该在编写一行代码之前就开始考虑质量问题。

软件质量不仅仅是编写测试和没有错误的软件。它可以应用于软件开发生命周期的所有阶段，并且可以包括从拥有一个工作的和容易建立的本地开发环境到自动化部署过程的广泛内容。

在接下来的部分中，我将谈论一些我认为从项目的第一天起就必须实现的主题，以便构建一个质量驱动的和可持续的软件项目。

每一部分都可以是一篇独立的文章，所以我会尽量把重点放在概念上，而不是在实现细节上输入太多。

**注意**:在这篇文章中，我会提到一些我喜欢的工具，你可以在开发生命周期的不同用例中使用它们。这些工具只是建议。本文的重点不是工具，而是过程和实践。使用您喜欢或熟悉的任何工具。

* * *

## 定义开发过程、工具等

你应该做的第一件事是定义你的开发过程看起来像什么，以及你将使用的主要工具和技术。

应用程序代码和工件存储在哪里？如何跟踪问题？向代码库添加新更改的过程会是怎样的？变更需要通过任何代码审查过程吗？哪些步骤是代码评审过程的一部分？将使用哪个 Git 流模型？谁可以贡献和合并代码？

所有这些问题都必须回答并记录在案。出于各种原因，良好的文档是至关重要的，我将在下一节中对此进行更多的讨论。

## 为优秀的文档建立基础

文档是非常重要的，但在软件开发中经常被视为次要任务。如果你在一个团队、开源项目中工作，或者如果你正在构建一个将被第三方使用的东西，例如:一个面向公众的 API，这就更重要了。

如果你从一开始就开始考虑文档，并定期做，这将是一个简单而愉快的任务。

首先，一个简单的“README”文件介绍项目，解释如何在本地运行它以及如何贡献，这是最起码的，但是您应该准备好记录从您的技术堆栈和工具、开发过程、编码标准、重要的架构和技术决策、API 端点等的一切。

你可能还想为你的终端用户写一些“产品手册”。

如果你把所有的事情都很好地记录下来，这不仅有助于在你的团队成员之间共享知识，确保每个人都是一致的，而且还会让任何新成员更容易加入到项目中，并立即开始为项目做出贡献。

首先，您应该找到一个存放所有文档的地方。它应该很容易添加/更新内容，每个人都可以访问。

对于技术性更强的文档，我认为最简单的方法是将 Markdown 文件写在项目报告的“docs”文件夹中。你可以用类似于 [VuePress](////vuepress.vuejs.org/) 或 [Gatsby](https://www.gatsbyjs.org/) 的东西把它转换成一个网站。这种方法的优点是文档更接近代码和版本控制，使得开发人员更容易保持更新。

对于更“通用”的或产品文档， [Confluence](https://confluence.atlassian.com/) 是企业中最流行的工具之一，但是有许多“wiki”系统可供您使用。[概念](https://www.notion.so/e566f30ed11148d8aa6ec12cae3a79f3)变得非常流行。[大纲](https://www.getoutline.com/)也是一个有趣的选择和它的开源。

如果你正在构建一个 API，看一看 [OpenAPI 规范](https://swagger.io/specification/)。

我更喜欢使用开放标准，因为它们有助于与其他工具集成，以构建强大的工作流并避免锁定，所以 Markdown 和 Open API 肯定是很好的选择。

## 定义并执行编码标准和准则

在团队中工作时，这一点尤其重要，有助于代码的一致性和可读性。

谁没有审查过一个 PR，这是一个非常小的功能，最后有很多更改的文件，因为开发人员将他们的编辑器配置为使用 2 个空间，而最初的开发人员使用 4 个空间？这将增加代码审查过程的大量开销和代码库中无用的更改。

你不需要重新发明轮子。大多数编程语言都有某种推荐的风格指南。PHP 有 [PSR-2](https://www.php-fig.org/psr/psr-2/) ，Javascript 有 [AirBnb](https://github.com/airbnb/javascript) 或[标准](https://standardjs.com/)，Python 有 [pep8](https://www.python.org/dev/peps/pep-0008/) 。

因为这些是流行的标准，所以您的开发人员很可能已经熟悉它们了。当然，这些只是指导方针，你可以根据自己的喜好随意修改。只要确保所有的事情都被很好的记录并得到你的团队的同意。

但是，如果不是每个人都遵循编码标准，那么拥有编码标准是没有用的。检查这些标准的使用永远不应该是一个手动过程，因为有许多工具可以自动完成。

用于 Javascript 的 ESLint 或用于 PHP 的 PHP_CodeSniffer 可用于检测违反代码标准的情况。一些工具如[漂亮的](https://prettier.io/)、 [gofmt](https://golang.org/cmd/gofmt/) 或 [PHP-CS-Fixer](https://github.com/FriendsOfPHP/PHP-CS-Fixer) 甚至可以根据定义的标准自动格式化你的代码并修复风格问题。

确保您的开发人员很好地理解这些工具，并让它们在他们的开发环境和 ide 中运行。您还应该在您的传递管道上启用这些检查。稍后将详细介绍。

确保在存储库中完成的任何提交都遵循指定准则的一个好方法是利用 [git 钩子](https://githooks.com/)在每次提交或推送时运行这些工具。我最近发现[预提交](https://pre-commit.com/)有助于在你的项目中设置 git 挂钩。你可能想试一试。

关于缩进规则和 tab vs spaces 的永恒争论，可以使用兼容所有流行代码编辑器的 [EditorConfig](https://editorconfig.org/) 。

你可以更进一步，通过定义提交消息或变更日志等其他东西的标准，使用像 [Commitizen](https://github.com/commitizen/cz-cli) 或[传统变更日志](https://github.com/conventional-changelog/conventional-changelog)这样的工具。

除了风格相关的标准，你还可以定义更多的建筑标准，比如说项目必须遵循“六边形建筑”。

这种标准很难自动执行，应该在结对编程或代码审查会议上检查。同样，如果你想让每个人都遵循这些，良好的文档和培训是必不可少的。

为了便于创建一个新项目，所有这些工具都已经配置好并准备好开始开发，您可以构建一些开发人员可以克隆的基本项目模板。GitHub 刚刚引入了[存储库模板](https://github.blog/2019-06-06-generate-new-repositories-with-repository-templates/)，你也可以使用像 [SAO](https://saojs.org/) 或 [Yeoman](https://yeoman.io/) 这样的工具来实现这个目的。

## 设置静态分析工具，及早发现代码气味

有许多因素会影响代码库的质量和可维护性。术语“代码味道”通常用来表示源代码中任何可能表明更深层次问题的特征。

死代码、代码重复和高度圈复杂度是应该尽快分析和修复的代码味道的好例子。

虽然编码标准保证了代码的一致性，但是静态分析可以检测代码库中潜在的结构问题。两者是互补的，是通向干净代码库的第一道关卡。

每种编程语言都有某种静态分析工具。花些时间研究并在您项目中配置它们。

如果你想更进一步，对你的项目代码质量有一个更详细的看法和度量，看看像 [SonarQube](https://www.sonarqube.org/) 、 [Code Climate](https://codeclimate.com/) 或 [Codacy](https://www.codacy.com/) 这样的工具。

虽然我不认为这些工具在开始时是必不可少的，但是它们可以提供一些额外的价值和质量相关度量的更好的可见性，这在更大的团队中尤其有用。

## 自动化测试套件

拥有一个强大的自动化测试套件是必不可少的，这不仅是为了避免错误，也是为了让你在进行重构和大的改变时更有信心。您对您的测试套件越有信心，您就能越快地将新特性部署到生产中，而不用担心退化。

像“连续交付”这样的实践只有在良好的自动化测试套件中才有可能。

自动化测试(更具体的单元测试)也是编写更好代码的强大驱动力，因为您将“被迫”编写更简单、更解耦的代码，并应用流行的设计模式，如依赖注入和其他 S.O.L.I.D 原则，以便它是可测试的。

测试也可以作为现有特性和所有可能的场景和用例的文档形式。

从单元测试开始，然后是集成/服务测试，并添加一些 E2E 测试，只是为了保证您的应用程序能够满足请求。

在开始添加特性之前，为每种类型创建一个简单“hello world”测试。这将保证您拥有执行这些测试所需的所有基础设施，从而在您不断开发新特性的同时，更快地添加更多的基础设施。

自动化测试是一个非常广泛和有趣的话题。如果您想更好地了解您的应用程序中应该有什么类型的测试以及如何组织它们，下面的文章是一个好的开始:

*   [实用测试金字塔](https://martinfowler.com/articles/practical-test-pyramid.html)
*   [结构化自动化测试指南| ThoughtWorks](https://www.thoughtworks.com/insights/blog/guidelines-structuring-automated-tests)
*   [软件测试反模式 Codepipes 博客](http://blog.codepipes.com/testing/software-testing-antipatterns.html)

## 开发环境

我在一些项目中工作过，对于任何一个新的开发者来说，仅仅是让项目在他的本地机器上运行就要花费几天的时间。文档的缺乏或过时以及工具的不足通常是造成这种情况的原因。

这个过程应该尽可能的简单和轻松，这样任何新的开发者都可以尽快开始开发。

我相信您应该能够配置一个开发环境，只需执行一个命令。多亏了像 [Docker](https://www.docker.com/) 和 [Docker Compose](https://docs.docker.com/compose/) 这样的工具，加上一个 Makefile 或一些 shell 脚本，现在做起来容易多了。

拥有一个自动化的、独立的项目设置还将保证所有的开发人员拥有相同版本的依赖项和运行时，从而减少由于不同版本而产生错误的机会。

您应该努力使您的本地环境尽可能接近生产环境。如果您在生产中使用 Docker，您可以利用 [Docker 多阶段构建](https://docs.docker.com/develop/develop-images/multistage-build)并使用您将在生产中使用的相同基础映像。

这种设置不是一次就能完成的。随着您添加新的服务或配置，它将需要定期维护。我建议您在应用程序中进行的任何需要更改本地开发脚本的更改都要立即进行，这样就不会被遗忘。参与项目的每个人都有责任保持项目最新。

如果你能在一台新机器(可以是虚拟机)上定期测试你的脚本，那就更好了。见鬼，您甚至可以添加一些自动化测试，至少检查所有应用程序服务是否正常启动。；)天空才是极限。

但是开发环境不仅仅是关于应用程序本身。花点时间微调你的代码编辑器/ IDE 和任何辅助工具(例如:实时重载)。许多 ide 都有配置文件，您可以通过版本控制与您的团队共享，因此全局配置在所有团队成员之间保持同步和一致。

## 输送管道

让我们来谈谈拼图中缺失的那块，我们将之前讨论过的所有东西粘在一起，这就是交付管道。

交付管道定义了代码从 VCS 提交到生产的所有阶段。

从一开始就正确配置基本的交付管道将确保您拥有一个稳定、自动化的机制来将应用交付给最终客户，并且任何代码都必须通过您定义的质量检查，才能部署到生产中。然后，您可以专注于构建功能，并更快、更高质量地交付。

管道的复杂程度因项目而异。首先，我认为必要的阶段是“构建阶段”、“静态分析阶段”、“单元测试阶段”、“集成测试阶段”、“验收阶段”和“部署阶段”。

稍后，根据您的需求，您可能会考虑添加更多阶段，例如安全性和性能测试。

### 建造阶段

这个舞台没什么特别的。这个阶段从版本控制系统中获取您的代码，并构建一个可部署的工件。它可以是 docker 映像、tarball、rpm 等，将生成的工件推送到某个工件存储库中。

### 静态分析阶段

这个阶段应该验证编码标准并运行静态分析检查来检测代码中的结构问题。我们在“编码标准”和“静态分析”章节中讨论的所有工具都应该在这个阶段执行。

### 单元测试阶段

这个阶段应该运行您的应用程序单元测试。即使您只是有一个“hello world”单元测试，在开始时，让它已经在管道中可用，将允许您在以后更快地构建它，因为运行测试的基础设施已经就绪。

### 集成测试阶段

这个阶段将运行您的集成测试(例如:数据库测试)或服务测试。

在这一阶段，您可能需要一些额外的服务，比如用于数据库集成测试的数据库，或者用于在服务测试期间清除应用程序外部依赖性的模拟服务器。

现在大多数流行的 CI / CD 工具都支持 Docker，所以很容易生成一个 MySQL 容器，例如，在测试中使用它。

### 验收阶段

在这个阶段，您的应用程序应该被部署到某个试运行环境中，在那里我们可以触发一些高级别的自动化测试，比如端到端测试(E2E)。

您还可以使用登台环境来进行一些手动的、探索性的或 UI 测试，或者共享任何正在进行的工作特性。

在自动化测试方面，根据[测试金字塔](https://martinfowler.com/bliki/TestPyramid.html)，你的重点应该是单元和集成测试。

首先，在这个验收阶段，一个简单的测试(检查您的应用程序在主页中返回 200 OK)和主用户流测试就足够了。该测试将捕获任何环境错误配置，并保证最终用户可以访问该应用程序。

### 部署阶段

在您的代码通过了前面的所有阶段之后，它应该可以部署到生产环境中了。

部署本身可以是手动或自动步骤。您可能不需要从第一天开始就创建这个 staging，但是您至少应该有一个 staging 环境，以便看到您的应用程序在本地环境之外的另一个环境中运行。

有很多很好的工具来定义你的交付管道。企业中仍然大量使用 Jenkins ，但我更喜欢更现代的工具，如 [CircleCI](https://circleci.com/) 、 [GitLab](https://about.gitlab.com/product/continuous-integration/) 或[无人机 CI](https://drone.io/) 。

GitLab 和 Drone 是开源的，你可以将它们安装在你自己的基础设施上，但是如果你刚刚开始，你应该寻找他们的云解决方案。GitLab 是一个一体化的解决方案，它提供了您所需要的一切，从版本控制、发行板、CI 管道、与 Kubernetes 的集成到部署等等，因为它是一个快速启动管道的伟大工具。

要了解如何正确构建持续交付渠道的更多信息，请阅读以下文章:

*   [连续输送管道 101 |大西洋](https://www.atlassian.com/continuous-delivery/pipeline)
*   [良好交付渠道的 5 个特征| ThoughtWorks](https://www.thoughtworks.com/insights/blog/5-traits-good-delivery-pipeline)

## 额外:寻找自动化机会

想想你做的每一个重复的手工过程，并寻找自动化的方法。从运行测试或下载数据库转储的 bash 脚本，到 GitHub WebHooks、聊天机器人，以及多种工具和 API 之间的集成。一切皆有可能。

* * *

## 结论

一个项目的可维护性和质量将会受到你启动它的方式的严重影响。

开始构建一个考虑质量的项目比以后再添加要容易得多。

定义你的软件开发生命周期，包括 git-flow，贡献指南，编码标准指南，拥有一个健壮的交付管道和优秀的文档是一个成功的、可维护的和无 bug 的项目必不可少的第一步。

各种项目都需要这些吗？像软件工程中的所有事情一样，没有“放之四海而皆准”的方法。有许多因素要考虑。很小的 app 还是 MVP？也许不是。一个项目，你想成为一个可靠的和/或有望在未来几年内发展壮大的项目。

对于 MVP 来说，尝试编写尽可能少的代码并利用现有的服务是一个很好的实践。然后，在你第一次发布之后，如果你的想法得到了验证，花时间按照这些实践从头开始重新构建它，而不是匆忙地在上面添加新功能。

如果需要几周以上的时间来完成，可能你在你的 MVP 上花了太多的时间，或者如果你的 MVP 本质上非常复杂，在这种情况下，在开始时“损失”一些时间，正确地设置它不会有太大的不同，并且如果你的项目在将来增长，会为你节省很多问题和麻烦。

有了一些经验和良好的工具，遵循本文中的步骤并不需要花费太多额外的时间和精力。

感谢阅读。

## 值得一读

*   高质量的软件值这个价钱吗？
*   [为什么我们总是从持续集成和持续交付开始](https://tailordev.fr/blog/2017/04/05/why-we-always-start-with-ci-cd/?utm_content=buffera2717&utm_medium=social&utm_source=twitter.com&utm_campaign=buffer)
*   [采用连续输送](https://www.youtube.com/watch?v=ZLBhVEo1OG4)
*   [为什么工程团队难以更快地部署？一个词:自动化](https://codefresh.io/continuous-deployment/engineers-struggle-with-ci-cd-automation-to-deploy-more-often/)
*   [基于主干的开发](https://trunkbaseddevelopment.com/)