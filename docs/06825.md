# 迄今为止最大的 React 原生启动器更新

> 原文：<https://dev.to/flatlogic/the-biggest-react-native-starter-update-so-far-5fkg>

我们在 [React Native Starter](https://reactnativestarter.com/) 中做了很多重大改动。事实上，我们从零开始做了一个全新的 React 原生启动器。几年后，我们可能会审视我们的工作，开发新的想法，并对我们的产品进行核心变革。我们认为这是遵循 React Native 自身的最佳实践和变化的唯一方式。架构和功能的修改帮助我们大幅缩短了未来移动应用的开发时间。我们认为速度是这种情况下最重要的标准。几乎我们所做的每一个改变都是为了让定制过程更快，或者减少你未来的移动应用程序中的错误数量。
[![react native starter](img/ad677171f99a18d430ece6af423a4ce5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--K93kjtKZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://flatlogic.com/blog/wp-content/uploads/2019/04/mailing-2-1-1024x683.png) 
在这篇文章中，我们想分享一些我们在创建第二版 [React 原生启动器](https://reactnativestarter.com/)时所做决定的想法。当然，我们感谢您在 GitHub 上的评论、电子邮件和提交。我们要感谢所有对 React Native Starter 第一版提供任何反馈的人。这是非常有意义和丰富的，更重要的是发人深省。

## **撤展转本土项目**

做出这个决定是因为世博会给你的项目增加了很多限制。我们使用 RNS 的主要目标之一是提供尽可能多的可伸缩解决方案。我们的初学者工具包的前一个版本是使用 Expo 框架构建的，这给我们的用户带来了一些限制。仅举几个例子:应用程序构建很大(普通项目超过 25mb)，缺乏本地模块，对离线功能的支持很差等等。此外，我们相信我们的初学者工具包可以用在复杂的应用程序中，您可能需要调整本机代码(Objective-C 或 Java ),而使用 Expo 无法做到这一点。我们从你，我们的用户那里收到了很多反馈，并决定在新版本中去掉 Expo。因为这个改变，2.0 版本没有第一个版本的向后功能。

## **增加了扑通发生器便于开发**

我们的另一个重要目标是在不影响质量的情况下尽可能加快开发过程。扑通是我们在这一领域的主要改进之一。首先，您可能已经知道我们的初学者工具包具有模块化架构(如果您不知道，请阅读我们的[文档)。](http://docs.reactnativestarter.com)下载我们的套件后，您想要添加您的定制模块和组件。使用我们的 Plop config，您只需运行一个命令就可以生成所有需要的文件和导入。只需在命令行中输入扑通一声，回答几个问题，您就可以拥有一个新的模块或组件了！神奇吧。

## **增加了对 Jest 和 Enzyme 单元测试的支持**

每个人都喜欢考试！嗯，你应该🙂通过我们的初学者工具包，我们希望帮助您交付尽可能稳定的代码。我们增加了两个选项来编写测试:用 Jest/Enzyme 工具箱进行单元测试，用 detox 进行 e2e 测试。让我们来谈谈第一个问题。Jest 和 Enzyme 是可以对移动应用进行单元测试的库。这些库可以帮助您通过编写简单而优雅的单元测试来测试您的组件和功能。Enzyme 增加了对快照测试的支持。

## **增加了对 e2e 排毒测试的支持**

E2e 指的是端到端测试，这意味着通过检查所有层、接口等来测试应用程序。以确保这些测试显示一个全面的 UX 图像。您可以为用户故事编写测试。例如，你为登录过程编写一个测试，它包含以下步骤:

*   打开一个 app。
*   输入电子邮件。
*   输入密码。
*   单击登录按钮。
*   验证主屏幕是否可见。

你使用 detox jest-wrapper 编写这些测试，你的应用程序在任何模拟器中打开，并根据你的所有故事进行测试。它有助于在开发过程中发现许多用户界面问题！

## **新的模块化架构将帮助您加速应用开发**

初学者工具包体系结构旨在支持可扩展的模块化应用程序。它是围绕 Redux 构建的，使推理应用程序的状态变得简单，结果是编写可维护的无错误程序。

请查看我们的文档，了解关于我们的[文档的更多信息。](https://docs.reactnativestarter.com/arhitecture)

## **总之**

那么下一步是什么？首先，我们将研究社区对此事的反馈。我们知道未来会有更多的变化，我们想下定决心处理所有的数据。第二，我们想收集一些基于我们产品的移动应用的例子。我们真的很感兴趣，你用我们的 [React Native Starter](https://reactnativestarter.com) v.2 创建了哪些移动应用。所以请继续关注我们的新闻，因为将会有几个引人注目的案例研究。我们认为，这个案例研究在几个方面确实具有启发性。

最初发表于[flatlogic.com](https://flatlogic.com/blog/the-biggest-react-native-starter-update-so-far/)

Flatlogic 创建了顶级的 Vue、Angular 和 React [管理模板](https://flatlogic.com/admin-dashboards)和最好的 React 原生[移动模板](https://reactnativestarter.com)。