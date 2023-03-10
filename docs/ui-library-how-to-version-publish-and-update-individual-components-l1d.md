# UI 库:如何版本化、发布和更新单个组件

> 原文：<https://dev.to/giteden/ui-library-how-to-version-publish-and-update-individual-components-l1d>

构建一个真正模块化的组件库，可以一次一个组件地开发和使用。

当您需要在不同的项目或应用程序之间共享通用的 UI 组件(React、Vue、Angular 等)时，您通常会构建一个共享的 UI 库。

这样做的主要好处是，您可以将组件分组在一起，避免多个存储库的巨大开销。问题是，当您开发、安装和更新它们时，它们仍然是分组的。

如果你有一个有效的方法来版本，发布，安装和更新单个组件，每个人的生活都变得容易得多。作为库的开发人员，您可以更自由地开发每个组件，并且可以控制库中开发的组件之间的依赖图。

安装库的人(你自己或其他应用程序开发人员)可以自由选择和使用他们真正需要的组件，并且只更新这些组件，而不是整个库。

[![](img/33c5e8ada64eaeeb2954280e164a56db.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bYEGE8Dh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2522/1%2ASqak1IDp4lXkfra06J2INw.png)

[![Left: [Grommet component library](https://github.com/grommet/grommet) in GitHub; Right: I[ndividual components published](https://bit.dev/grommet/grommet) from the library.](img/e98746462ab8437b7cfbdd122a6c4690.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--K_TTIo63--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2722/1%2AvBaP75fy4x6nIj7yi1dBeA.png) *左:[GitHub 中的索环组件库](https://github.com/grommet/grommet)；右图:我从库中发布了个组件。*

在这篇文章中，我们将学习如何使用像 [Bit](https://bit.dev) 这样的面向组件的工具来实现这一点，而不必开始重构你的库。它还将为您的库提供其他好处，比如组件的可发现性和为不同的团队/项目组织共享集合的能力。

## 为什么用单个组件开发？

**开发**:您可以利用[位](https://bit.dev)将每个组件隔离为您库中的一个独立模块，包括它的所有文件和依赖项。您可以单独或一起对每个组件进行版本控制。由于 Bit 允许您控制项目中所有组件的依赖图，因此您可以开发每个组件，并准确地了解您所做的更改如何影响 repo 中的其他组件，因此您可以更新所有需要更新的内容。然后，您可以单独发布每个组件，以便在其他地方重用。

**消耗**:你和其他人可以使用 npm/yarn 作为独立的软件包单独安装每个组件。当消费项目需要更改时，您可以将任何组件的源代码直接导入到该项目中进行更改。然后，你可以更新和分享一个新的版本。这意味着作为一个团队使用跨仓库协作工作流。

**更新**:消费者可以只获得他们使用的组件的更新，而不必带来整个库的更新。他们还可以建议对任何组件进行更新，而无需深入库。这意味着使用组件的开发人员会更多地采用您的库。

**可发现性**:您可以创建一个共享的 [bit.dev](https://bit.dev) 集合，在这里您的所有组件都可供每个人发现、使用和协作。这甚至不仅仅局限于开发人员，因为你可以[与其他人](https://blog.bitsrc.io/let-marketing-and-developers-collaborate-over-components-b0e5e8c111ee)分享组件，比如营销、产品、设计等，以学习你所拥有的。您可以呈现每个组件，使用示例，查看自动提取的 API 引用，为每个组件运行独立的构建/测试过程等等。

## 从库中隔离、版本化和发布单个组件

<center>

让我们用 [Bit](https://github.com/teambit/bit) 来模块化我们库中组件的开发和分发。它将为我们做繁重的工作，并允许我们随时控制每个组件的依赖图的确切状态。

这是一个为共享组件而构建的工具。让我们用它来跟踪我们库中的不同组件。它将分析每一个组件，并将它与所有组件的文件和依赖项打包在一个隔离的容器中。然后，我们可以对每个组件分别进行版本控制、发布和更新。

首先，让我们快速为您的库设置 Bit。

安装 Bit 并为您的资源库项目创建工作空间:

```
$ npm install bit-bin -g

$ cd project-directory
$ bit init 
```

然后前往 [bit.dev](https://bit.dev/) 并快速创建一个[免费账户](https://bit.dev/signup?redirectUri=%2F&source=header_button)。然后，[创建一个集合](https://bit.dev/~create-collection)来托管您的组件(并使它们可被发现)。

完成后，向您的 bit.dev 帐户验证 bit:

```
$ bit login 
```

太好了！现在您已经准备好开始跟踪组件了。使用 bit add 命令[告诉 bit 在库](https://docs.bit.dev/docs/add-and-isolate-components.html)中的何处找到组件。

例如，让我们跟踪 src/components 中的所有组件。在这种情况下，我们将使用 glob 模式告诉 Bit 跟踪这个目录中的所有组件。

```
$ bit add src/components/* 
```

现在运行 bit status，查看组件是否被跟踪，该位没有丢失任何组件的文件或依赖项。

接下来，让我们[导入一个编译器](https://bit.dev/bit/envs)来将代码构建到可以在其他项目中运行的文件中。例如，让我们在工作区中安装一个 [React 编译器](https://bit.dev/bit/envs/compilers/react):

```
$ bit import bit.envs/compilers/react 
```

您现在可以运行 bit build 来查看一切工作正常。

接下来，让我们[为组件](https://docs.bit.dev/docs/tag-component-version.html)设置一个版本。Bit 会用一个次要版本来版本化每个组件，但是你可以覆盖并选择你自己的版本。为每个组件设置一个独立的版本对于单独开发、发布、安装和更新它们非常有用。

首先，让我们将所有组件设置为 1.0.0 版。稍后，我们可以使用 Bit 来更新单个组件，同时控制它们之间的依赖关系。

```
$ bit tag --all 1.0.0 
```

接下来，运行快速位状态以

现在您已经准备好将组件发布到 bit.dev:

```
$ bit export <your-user-name>.<your-collection-name> 
```

就是这样！现在，请前往您的收藏，探索其中的组件。现在可以为每个组件添加一个保存的示例，bit.dev 将为组件呈现这个示例。这些示例还将帮助生成组件集合中的 snaphot preview bit.dev，以帮助您进行选择。

例如，如果你共享了 [semantic-ui-react 库](https://github.com/Semantic-Org/Semantic-UI-React)，你可以把它变成:

[![](img/638165e779912f1313f00af924e48c1f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SICX_-BJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2046/1%2AbFKJIcfLPtA1DdfBNn3V1Q.png)

这个单独可重用组件的集合。请注意，您现在甚至可以看到每个组件的确切包大小和依赖关系。

[![](img/c9d60602f943f45a8c3ef608fcf43d63.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--x9bzCZKN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2732/1%2AJQEfhelr5z4jKwnZbXMjhg.png)

这里有一个组件页面的例子，你可以玩代码，看组件渲染，用 npm/yarn 安装组件。

[![](img/32f9bd0cf4f4c77de202bcc10b1983ae.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4yLW0hfu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2704/1%2AhICSnsSL1ogTkyyGyiJv_A.png)

接下来，让我们看看如何在不同的项目中安装和更新单个的组件，而不必引入整个库。

## 从库中安装单个组件

当您在 [bit.dev](https://bit.dev) 中进入一个组件的页面时，您可以看到有一个窗格，其中显示了使用该组件的不同方式。让我们从 semantic-ui-react 来看转换组件:
[**transition-semantic-ui-react Bit**
*在每个转换上将可见性更改为已显示的回调。标签:反应，过渡。依赖关系:类名…*bit.dev](https://bit.dev/semantic-org/semantic-ui-react/transition)

在 bit.dev 页面中，您可以看到它只有 73k 重，并且有一些依赖项，包括 Lodash。这有点多，但至少现在在我真正安装组件之前，我确切地知道我要添加到我的应用程序中的是什么。

如果您已经运行了 bit log in，那么您已经将 npm 客户端配置为 bit.dev 作为作用域注册表。如果没有，请运行以下命令:

```
npm config set '@bit:registry' https://node.bit.dev 
```

然后，只需使用 npm/yarn 安装组件:

```
npm i @bit/semantic-org.semantic-ui-react.transition

# or

yarn add @bit/semantic-org.semantic-ui-react.transition 
```

仅此而已。我只将组件添加到我的应用程序中，而不必安装整个库。例如，在您的组织内，这有助于提高您构建的共享库的采用率，并保持您的应用程序快速而苗条。

## 更新库中的单个组件

当您对库中的组件进行更新时，您可以再次标记它，然后将其作为新版本导出到您的 bit.dev 集合中。在此之前，您甚至可以运行`bit status`来了解哪些其他相关组件将会受到这些变化的影响——以及如何影响。

然后，组件的消费者可以只更新他们实际使用的组件，而不必更新整个库。

此外，使用组件的人可以使用 bit import 命令[将组件的实际源代码带入任何 repo](https://docs.bit.dev/docs/sourcing-components.html) 。在那里，他们可以在本地[对代码](https://docs.bit.dev/docs/modifying-sourced-components.html)进行修改，并标记一个新版本。新版本可以留在本地，也可以共享回集合。导出时，可以传递- eject 标志，将修改后的组件移回 package.json 文件中的包依赖项。

然后，更新后的版本可以被引入到任何其他 repo 中，包括原始库。由于 Bit 在幕后使用 Git，所以您可以使用 [Git 的合并实用程序来合并同一个本地 Bit 工作区中组件的不同版本](https://docs.bit.dev/docs/merge-changes.html)之间的变更，从而轻松地在存储库之间同步代码变更。

随着即将到来的 GitHub-sync 集成，他们也可以使用这个工作流向您在 GitHub 中的库发出 pull 请求。这有助于采用您的库，并为每个人提供协作工作流。

## 组件发现和协作

[![](img/555720a80cef8e64d49694dcb97c6404.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SQs1kGuO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2APM2IyZdp4WRi_CE7r4Dz8A.gif)

现在您已经将您的库共享到 bit.dev，您的集合变成了一个共享门户，您的团队可以通过它共享可重用的组件。

您可以通过语义搜索来搜索和探索组件，过滤相关参数，如上下文标签或组件包大小，并让不同的人和团队在您共享的组件上进行协作。

[![Example of a React Pacman loader component in the bit.dev editable playground](img/a75a145ecd654ce0b3f8a55b8c7ee1b8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gxVoGLZI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AF_ozsf8lx04FUu87xzqO8w.gif)*bit . dev 可编辑游乐场中 React Pacman 加载程序组件的示例*

库开发人员可以更好地控制库中组件的开发和分发，并增强对其组件的可发现性和协作。组件消费者可以轻松地找到并安装组件，同时从他们的项目中获取并建议更新。

这意味着您可以通过跨不同项目发布、安装和更新的模块化组件来更快地构建应用程序。希望这能有所帮助，所以[可以随意加入并尝试一下](https://bit.dev) **或** [探索教程](https://docs.bit.dev/docs/bit-react-tutorial.html)。**干杯，请不要犹豫，提出以下任何问题👐

</center>