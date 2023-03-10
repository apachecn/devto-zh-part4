# 人工智能组件建模的 10 个工具——无需代码的机器学习

> 原文：<https://dev.to/jcabot/10-tools-for-modeling-ai-components-machine-learning-without-the-code-4dhi>

人工智能(AI)有无数的库、平台和基于云的服务。但是直接在它们之上编程你的 AI 应用程序会使你的软件过于依赖你选择的特定基础设施。在这样一个每天都有新的(更好的)人工智能解决方案涌现的快节奏环境中，这是很危险的。

你如何开发人工智能增强软件(也称为[智能应用](https://www.information-age.com/ai-assistant-application-development-123473071/))或纯人工智能组件(神经网络、图像识别软件、聊天机器人...)来解决一个数据科学问题，而不学习每一种特定的人工智能技术和语言，并且保留我在新的人工智能基础设施上轻松迁移我的软件的能力？这个解决方案不会让我的读者感到惊讶: **[建模 AI](https://modeling-languages.com/modeling-and-artificial-intelligence-opportunities/) 前来救援。**

通过提高你定义的人工智能需求的抽象层次，你可以首先关注人工智能行为的建模，然后对其进行提炼，以集成特定平台的细节。正如你刚才看到的，这是所有人工智能提供商现在都在追随的趋势。

让我们仔细看看我所知道的最好的建模 AI 工具。我试图涵盖几种工具:从完整的数据科学建模到人工智能的代码生成。

## 来自大型科技公司的人工智能可视化建模工具

大玩家(谷歌、亚马逊、微软)正在提供可视化人工智能建模环境，作为一种将更多用户带到他们的机器学习解决方案的方式。他们“销售”它们作为一种机器学习的可视化编程或“没有代码的机器学习”，试图吸引对 ML 感兴趣但害怕编写代码来创建和训练 ML 模型的人。当然，这些模型只能在各自厂商的专有基础设施上执行。

### Azure 机器学习工作室

我最喜欢的 ML 建模环境是微软的 [Azure 机器学习工作室](https://azure.microsoft.com/en-us/services/machine-learning-studio/)。正如微软所说:“这是一个非常简单的基于浏览器的可视化拖放创作环境，不需要任何编码”。有了它，你可以很容易地定义输入数据，处理它(如有必要)，并使用它来训练几种类型的 ML 模型和评估结果的质量。

[![Building a classifier model with Azure ML Studio](img/6e345dcd32211d5963a0eb94715deba4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DfZ-U02d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://azurecomcdn.azureedge.net/cvt-38ff2bbd6f3685d423d85ec5fffdc68d952f70196ec3dbb12bec48314847ecimg/page/services/machine-learning-studio/simple-scalable-cutting-edge.jpg)

### 来自 IBM 的 SPSS Modeler

Azure ML Studio 的 IBM 替代品是 [SPSS Modeler，](https://www.ibm.com/products/spss-modeler)是 [Watson Studio](https://www.ibm.com/cloud/watson-studio/pricing) 的一部分。与上面的微软竞争对手类似，您可以定义您的输入数据管道，您想要生成的模型(分类器，预测，...)并评估和可视化结果的质量。它带有完整的算法和预定义的模型，可立即用于引导您自己的数据科学应用。

[![](img/841191eba378eac61d24fa1b814ff489.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JAQAYkk2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2Ahl1vCyDOFn7PYqKF70WH1Q.png)

亚马逊最接近的解决方案是[亚马逊 SageMaker](https://aws.amazon.com/sagemaker/) ，但我一直无法在其中找到一个可视化建模人工智能编辑器。

## 数据科学建模环境

在数据科学中，数据收集和处理方面与对数据执行的学习/预测过程一样重要。数据科学的复杂性在很多时候在于决定收集什么数据以及数据的什么特征对于我们想要解决的预测问题是重要的。一些数据科学工具提供了自己的运行时环境，但许多工具内置了与深度学习框架和库的集成，如 [Keras](https://keras.io/) 或 [Tensorflow](https://www.tensorflow.org/) 。

### RapidMiner

[RapidMiner](https://rapidminer.com/) 配有可视化工作流设计器，通过预定义的连接(包括许多用于数据采集的连接，RapidMiner 包括 60 多种用于结构化和非结构化数据的文件类型和格式)、内置模板和可重复的工作流来加速预测模型的原型制作和验证。

### 橙色

[Orange](https://orange.biolab.si/) 是一个开源的机器学习和数据可视化工具包。数据分析是通过链接工作流中的小部件来完成的。每个小部件可以嵌入一些数据检索、预处理、可视化、建模或评估任务。有大量预定义的小部件可用，但是您也可以构建自己的小部件。

### 刀 knime

我最喜欢的中小企业选择。Knime 是一个通用的数据分析平台，可用于多种任务。Knime 提供了 2000 多种不同类型的节点来满足您的所有需求。用于数据科学家的[Knime](https://www.knime.com/knime-for-data-scientists)和用于深度学习的[Knime](https://www.knime.com/deeplearning)扩展是这篇文章主题中最有趣的。例如，后者允许用户读取、创建、编辑、训练和执行深度神经网络。由于所有节点都可以组合，因此您可以轻松地将深度学习节点用作任何其他类型的数据分析项目的一部分。

最重要的是， [Knime 是开源的](https://github.com/knime/)并且是免费的(你可以创建和购买商业插件)。

[![Knime modeling environment](img/c5b8c409c4e8a88a90ba89067dfcee35.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Mzlj4Hvj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://files.knime.com/sites/default/files/inline-images/login.png)

请注意，除了这些数据科学工具，其他泛型环境正在获得数据科学扩展。一个例子是 [Neuron](https://marketplace.visualstudio.com/items?itemName=neuron.neuron-IPE) ，Visual Studio 代码的[数据科学扩展。](https://blogs.msdn.microsoft.com/uk_faculty_connection/2018/10/29/data-science-in-visual-studio-code-using-neuron-a-new-vs-code-extension/)

### 大台库

更面向大型企业，[data aiku](https://www.dataiku.com/)旨在将在数据科学项目中发挥作用的每个人(业务分析师、数据科学、数据工程师、...)在一个单一平台中。Dataiku 集成了大量其他工具，从笔记本到用于数据可视化的图表库，当然还有所有主要的 ML 库。

一旦建立了管道，就可以通过 REST API 将其打包成一个可部署的包，用于实时预测。

## 神经网络建模

如果你的主要兴趣是神经网络本身的建模， [DIANNE](http://dianne.intec.ugent.be/) 是个不错的选择。在 Dianne 中，神经网络被构建为有向图。DIANNE 附带了一个基于 web 的 UI 生成器，可以拖放神经网络模块并将它们链接在一起。

[![Visual modeling of neural networks](img/74213703bba2f35ad5da8f2d23dafcbb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EM0v8DWz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://dianne.intec.ugent.img/screenshot-build.png)

要了解神经网络如何工作，这个 [Tensorflow 游乐场](https://playground.tensorflow.org/)是玩神经网络和学习其基本概念的理想选择

[![Visual modeling and execution of neural networks with TensorFlow](img/c39016d86103d3e1d2e5bc6b5811e54e.png)](http://modeling-languages.com/wp-content/uploads/2018/12/tensorflow.png)

## 可视化学习到的模型

一些工具专注于结果的可视化，以此来帮助你理解你创建的深度学习模型的质量。例如， [TensorBoard](https://www.tensorflow.org/guide/summaries_and_tensorboard) 是一套用于检查 TensorFlow 运行的 web 应用程序。Nvidia 建立在 TensorBoard 之上，在其 [Nvidia Digits](https://developer.nvidia.com/digits) 工具中可视化深度神经网络。

[![Visual deep learning modeling in Nvidia](img/9c788431aa1251b0ed4b1eb7474cc937.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--x9ltw-Rd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.nvidia.com/sites/default/files/akamai/cuimg/deeplearning/Visualize_model.png)