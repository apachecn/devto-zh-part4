# 递归神经网络:语言建模的发电站

> 原文：<https://dev.to/adamcalica/recurrent-neural-networks-the-powerhouse-of-language-modeling-gd7>

大学三年级春季学期，我有机会去丹麦哥本哈根留学。在那之前我从未去过欧洲，所以我非常兴奋地沉浸在一种新的文化中，结识新的人，去新的地方旅行，最重要的是，接触一种新的语言。现在，虽然英语不是我的母语(越南语是)，但我从小就学习和说英语，使它成为我的第二天性。另一方面，丹麦语是一种极其复杂的语言，有着非常不同的句子和语法结构。在我旅行之前，我试图用 Duolingo 应用程序学一点丹麦语；然而，我只掌握了一些简单的短语，如你好(Hej)和早上好(God Morgen)。

当我到了那里，我不得不去杂货店买食物。嗯，那里所有的标签都是丹麦文的，我好像看不出来。在努力寻找全麦面包和小麦面包之间的区别长达半个小时后，我意识到不久前我已经在手机上安装了谷歌翻译。我拿出手机，打开应用程序，将摄像头对准标签……瞧，这些丹麦文字立刻被翻译成了英语。事实证明，谷歌翻译可以翻译摄像头看到的任何单词，无论是街道标志，餐馆菜单，甚至是手写数字。不用说，这个应用程序在我出国留学期间节省了我大量的时间。

[![](img/44aa0b54fa7e0bc06dedb703203f918b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cU4KRg1---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bk0hkifdev9cfqd1r5ld.png)

Google Translate 是谷歌 [<u>自然语言处理研究小组</u>](https://research.google.com/pubs/NaturalLanguageProcessing.html) 开发的产品。这个小组专注于跨语言和跨领域大规模应用的算法。他们的工作跨越了传统 NLP 任务的范围，用通用的语法和语义算法来支撑更专业的系统。T9】

从更广阔的层面来看，NLP 位于计算机科学、人工智能和语言学的交汇点。目标是让计算机处理或“理解”自然语言，以便执行有用的任务，如情感分析、语言翻译和问题回答。充分理解和表现语言的意义是一个非常困难的目标；因此，据估计，完美的语言理解只能由人工智能完成的系统来实现。了解 NLP 的第一步是语言建模的概念。

## 语言建模

语言建模是预测下一个单词是什么的任务。例如，给定句子“我正在写一个……”，接下来的单词可以是“信”、“句子”、“博文”……更正式地说，给定单词序列 *x(1)、x(2)、…、x(t)、* 语言模型计算下一个单词 *x(t+1)的概率分布。*T9】

最根本的语言模型是 **n 元模型** 。一个 n-gram 是一个由 n 个连续单词组成的组块。例如，给定句子“我正在写一个……”，那么下面是相应的 n 元语法:

*   **【我】【是】【写作】【一】T5】**

***   **连词:** “我是”、“正在写作”、“正在写作”

    *   **卦:** 【我在写】【正在写一个】

    *   **4-克:** “我在写一个”** 

 **n-gram 语言建模背后的基本思想是收集关于不同 n-gram 出现频率的统计数据，并使用这些数据来预测下一个单词。然而，n-gram 语言模型存在稀疏性问题，即我们在语料库中没有观察到足够的数据来准确地对语言建模(特别是当 n 增加时)。T3】

[![](img/a07520ab02f0d7d01980595902926142.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lMkYyznU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1o0wihnkp477389l8yji.png)

代替 n-gram 的方法，我们可以尝试一种基于**的神经语言模型，如 *前馈神经概率语言模型* 和 *递归神经网络语言模型* 。 这种方法通过将单词表示为向量(单词嵌入)并将其用作神经语言模型的输入来解决数据稀疏问题。这些参数是作为训练过程的一部分学习的。通过神经语言模型获得的单词嵌入展示了这样的性质，即语义上接近的单词在诱导向量空间中同样接近。此外，递归神经语言模型还可以捕获句子级、语料库级和子词级的上下文信息。T19】**

 **### **递归神经网络语言模型**

RNNs 背后的思想是利用顺序信息。rnn 被称为 **循环** ，因为它们对序列的每个元素执行相同的任务，输出取决于之前的计算。理论上，rnn 可以利用任意长序列中的信息，但从经验上来说，它们仅限于回顾几个步骤。这种能力允许 rnn 解决诸如未分段的、连接的手写识别或语音识别之类的任务。

我们来打个比方。假设你正在看*(顺便说一句，一部现象级的电影)。电影中有如此多的超级英雄和多重故事情节，这可能会让许多事先不了解漫威电影宇宙的观众感到困惑。然而，你有正在发生的事情的背景，因为你已经按照时间顺序看过了以前的漫威系列(钢铁侠，雷神，绿巨人，美国队长，银河护卫队)，能够正确地联系和连接一切。这意味着你记得你看过的所有东西，以理解无限战争中发生的混乱。*

 *[![](img/a6dcb71e6fa4b3c5bdcb43190abd1427.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YRdZEa6x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3mb7j90vyntvn4wkbp2p.jpg)

同样，RNN 记得所有的事情。在其他神经网络中，所有的输入都是相互独立的。但是在 RNN，所有的输入都是相互关联的。假设您必须预测给定句子中的下一个单词，所有前面单词之间的关系有助于预测更好的输出。换句话说，RNN 在训练自己的时候记住了所有这些关系。T3】

RNN 使用一个简单的循环来记住它从先前的输入中所知道的。该循环从先前的时间戳中获取信息，并将其添加到当前时间戳的输入中。下图显示了基本的 RNN 结构。在特定的时间步长**，**【X(t)**是网络的输入，**【h(t)**是网络的输出。 **A** 是 RNN 细胞，它包含神经网络，就像一个前馈网络。T19】**

 **[![](img/9180362e48989da5aafeac3ed0432872.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2Gz6Cpgg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sx5cmacmh9rrg5icktfh.png)

这种循环结构允许神经网络接受输入的序列。如果你看到下面展开的版本，你会更好的理解:T3】

[![](img/72bed8f9179b36e5326c80ad3a5cc8c8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jSTjpdkr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mybz1vun0i7vbk1utk2i.png)

首先，RNN 从输入序列中取出 **X(0)** ，然后输出 **h(0)** ，与 **X(1)** 一起作为下一步的输入。接下来，**【h(1)**从下一步开始输入用**【X(2)进行下一步，以此类推。通过这个递归函数，RNN 可以在训练时记住上下文。**

 **如果你是个数学呆子，很多 rnn 都用下面的等式来定义自己隐藏单元的值:

[![](img/d3552b1b8acbc0c21cece08ea58aa246.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9VHPOJlr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kyeil4gjxjgade0zy4zn.png)

其中 **h(t)** 为时间戳 t 时的隐藏状态， **∅** 为激活函数(Tanh 或 Sigmoid)， **W** 为时间戳 t 时输入到隐藏层的权重矩阵，**【t】**为时间戳 t 时的输入， **U** 为

RNN 利用反向传播通过训练学习权重****W**。这些权重决定了先前时间戳的隐藏状态的重要性和当前输入的重要性。本质上，它们决定了应该使用隐藏状态和当前输入中的多少值来生成当前输入。激活函数 **∅** 将非线性添加到 RNN，从而简化了用于执行反向传播的梯度计算。T15】**

 **### **RNN 劣势**

rnn 并不完美。它有一个主要的缺点，被称为 **消失梯度问题，** 这妨碍了它的高精度。随着上下文长度的增加，展开的 RNN 中的图层也会增加。因此，随着网络变得更深，反向传播步骤中回流的梯度变得更小。结果，学习速度变得非常慢，并且不可能期望语言的长期依赖性。换句话说，RNNs 在记忆序列中非常远的前几个单词时会遇到困难，并且只能根据最近的单词进行预测。

[![](img/6eba111b38f9e90c87ffd11c46c2a33f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ax01tBTe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vlw6b52twy1vmf0fnvd2.png)

### **RNN 扩展**T3】

多年来，研究人员开发了更复杂类型的 RNN 来处理标准 RNN 模型的这一缺点。让我们简单回顾一下最重要的:

*   [**<u>双向 rnn</u>**](https://ai.intel.com/wp-content/uploads/sites/53/2017/06/BRNN.pdf)只是由 2 个 rnn 叠加而成。然后基于两个 rnn 的隐藏状态合成输出。其思想是，输出可能不仅取决于序列中先前的元素，还取决于未来的元素。T9】

*   [**<u>长短期记忆网络</u>**](http://www.felixgers.de/papers/phd.pdf) 这几天挺流行的。除了隐藏状态之外，它们继承了标准 rnn 的确切架构。LSTMs 中的存储器(称为 **单元** )将先前状态和当前输入作为输入。在内部，这些细胞决定记忆中保留什么和清除什么。然后，它们将先前的状态、当前的记忆和输入结合起来。这个过程有效地解决了消失梯度问题。T13】

*   [**<u>门控递归单元网络</u>**](https://pdfs.semanticscholar.org/2d9e/3f53fcdb548b0b3c4d4efb197f164fe0c381.pdf) 用门控网络扩展了 LSTM，该门控网络产生信号，该信号用于控制当前输入和先前存储器如何工作以更新当前激活，从而更新当前网络状态。门本身是加权的，并根据算法有选择地更新。T9】

*   [**<u>神经图灵机</u>**](https://arxiv.org/pdf/1410.5401.pdf) 通过将它们耦合到外部存储器资源来扩展标准 rnn 的能力，它们可以通过注意力过程与之交互。这类似于艾伦·图灵(Alan Turing)用无限存储带丰富有限状态机。T11】

了解更多关于 [<u>递归神经网络</u>](https://builtin.com/data-science/recurrent-neural-networks-powerhouse-language-modeling) 的内置。T9】***********