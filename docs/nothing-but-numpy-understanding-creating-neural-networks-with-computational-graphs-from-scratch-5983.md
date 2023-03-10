# 只有数字:从零开始理解和创建带有计算图的神经网络

> 原文：<https://dev.to/rafayak/nothing-but-numpy-understanding-creating-neural-networks-with-computational-graphs-from-scratch-5983>

理解新概念可能会很难，尤其是现在有大量的资源，而对复杂的概念只有粗略的解释。这篇博客是缺乏如何以计算图的形式创建神经网络的详细演练的结果。

在这篇以及后面的一些博文中，我将把我所学到的东西整合起来，作为一种回馈社区和帮助新加入者的方式。我将创造普通形式的神经网络，只需要借助于 [NumPy](//www.numpy.org) 。

这篇博文分为两部分，第一部分是理解神经网络的基础知识，第二部分是实现第一部分所学内容的代码。

* * *

## 第一部分:了解神经网络

### [让你进入](#lets-dig-in)

神经网络是一种受大脑工作方式启发的模型。与大脑中的神经元类似，我们的“数学神经元”也是直观相连的；它们接受输入(树突)，对其进行一些简单的计算，然后产生输出(轴突)。

学习某样东西的最好方法是去构建它。先从一个简单的神经网络入手，手动求解。这将让我们了解计算是如何通过神经网络的。

<figure>[![Fig 1\. Simple input-output only neural network](img/a192cdaaadf2d560af93a5fa3054381c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--womYlIIE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dg7wckx2vkpsqkpcmztu.png) 

<figcaption>图 1。简单的仅输入输出神经网络</figcaption>

</figure>

如上图所示，大多数时候你会看到一个类似的神经网络。但是这张简洁简单的图片隐藏了一点复杂性。让我们把它展开。

<figure>[![](img/06a452e163e259e448aeb6b5b794a808.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--k1f9lI9G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/trcf17nn78z21xmtzffb.png) 

<figcaption>图 2。扩展的神经网络</figcaption>

</figure>

现在，让我们仔细检查图表中的每个节点，看看它代表什么。

* * *

<figure>[![](img/668b637cf17aa9fab726595410f040d1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MDHSlkUM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xc990gikalygp2g11tu0.png) 

<figcaption>图 3。输入节点 *x₁* 和 *x₂*</figcaption>

</figure>

这些节点代表我们的第一个和第二个特征的输入，***【x₁】***和***【x₂】***，它们定义了我们馈送给神经网络的单个示例，因此称为 ***【输入层】***

<figure>

[![](img/8681a9ddbb35e38fe85decf5ea911557.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--suhaNHrL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uez5ds02tolxjm23c5jj.png)

<figcaption>Fig 4\. Weights</figcaption>

</figure>

**代表我们的权重向量(在一些神经网络文献中用θ符号、 ***θ*** 表示)。直观地说，这些决定了每个输入要素在计算下一个结点时的影响程度。如果你是新手，可以把它们想象成线性方程中的“斜率”或“梯度”常数。**

 **权重是我们的神经网络必须“学习”的主要值。因此，最初，我们将把它们设置为随机值*，并让我们的神经网络的“学习算法”决定产生正确输出的最佳权重。*

 *为什么随机初始化？稍后将详细介绍。

<figure>

[![](img/3722f94bf65573c8e979ac5905465609.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--O5x7F-YL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d3oh7wlxb4nae0dvs9dr.png)

<figcaption>Fig 5\. Linear operation</figcaption>

</figure>

这个节点代表一个线性函数。简单地说，它接受所有的输入，并从中创建一个线性方程/组合。(按照惯例，可以理解，除了输入层中的输入节点之外，权重和输入的线性组合是每个节点的一部分，因此该节点在图中经常被省略，如图 1 所示。在本例中，我将把它留在这里)

<figure>

[![](img/f0ce5646c5ee37a6b9adc286585dac5b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FvPsKoyh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6wze2pnuj3fpcp94rjqk.png)

<figcaption>Fig 6\. Linear operation</figcaption>

</figure>

这个***【σ】***节点接受输入并将其传递给以下函数，称为【This 形函数(因为其曲线呈 S 形)，也称为*逻辑函数*:

<figure>

[![](img/53552006206a1f0ef72414fef955c969.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tUeIiQul--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/038gcuo063bf1zzdvhnq.png)

<figcaption>Fig 7\. Sigmoid(Logistic) function</figcaption>

</figure>

Sigmoid 是神经网络中使用的许多“激活函数”之一。激活功能的作用是将输入改变到不同的范围。例如，如果 z > 2，则σ(z) ≈ 1，类似地，如果 z < -2，则σ(z) ≈ 0。因此，sigmoid 函数将输出范围压缩到(0，1)(这种'()'符号意味着独占边界；当函数渐近线时，从不完全输出 0 或 1，而是非常接近边界值)

**在我们上面的神经网络中由于它是最后一个节点，所以它执行输出**的功能。预测输出用 ***、ŷ*** 表示。(注意:在一些神经网络文献中，这由***‘h(θ)’***表示，其中‘h’被称为假设，即这是神经网络的假设，也称为输出预测，给定参数θ；其中θ是神经网络的权重)

* * *

现在我们知道了每一个和每一件事物代表什么，让我们通过手动计算一些虚拟数据上的每个节点来展示我们的肌肉。

<figure>

[![](img/4f33d5b51e8ac162cbef5b66a6326f65.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--V0oS50F0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zrgio4rdgugo3wejmodl.png)

<figcaption>Fig 8\. OR gate</figcaption>

</figure>

以上数据代表一个**或**门(如果任何输入为 1，则输出为 1)。表格中的每一行都代表了我们希望神经网络学习的一个“例子”。在从给定的例子中学习之后，我们希望我们的神经网络执行或门的功能；给定输入特征，**，试输出相应的***【y】(也叫‘标签’)***。我还在二维平面上绘制了这些点，以便于可视化(绿色十字表示输出( **y** )为 ***1*** 的点，红点表示输出为 ***0*** 的点)。**

 **这个或门数据特别有趣，因为它是可线性分离的*，也就是说，我们可以画一条直线来分离绿色十字和红色圆点。*

 *<figure>

[![](img/f95c31be205666f4686d96c9edf35997.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tFeTT5Bs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hpcbca884v4wahor0m2e.png)

<figcaption>Fig 9\. Showing that the OR gate data is linearly separable</figcaption>

</figure>

我们将很快看到我们简单的神经网络如何执行这项任务。

在我们的神经网络中，数据从左向右流动。用专业术语来说，这个过程叫做**‘正向传播’**；来自每个节点的计算被转发到它所连接的下一个节点。

让我们仔细检查一下我们的神经网络将对给定的第一个例子***【x₁=0***和***【x₂=0***执行的所有计算。同样，我们将初始化权重***【w₁】***和 ***w₂*** 到 ***w₁=0.1*** 和 ***w₂=0.6*** (回想一下，这些权重 a 是随机选择的)

<figure>

[![](img/4ce7e69a3984d04bb7ebde9d6be248a3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IERWk0UR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fklrrwsvg0alxem7zqds.png)

<figcaption>Fig 10\. Forward propagation of the first example from OR table data</figcaption>

</figure>

以我们目前的权重， ***w₁= 0.1*** 和 ***w₂ = 0.6*** ，我们的网络输出离我们想要的位置有点远。预测的输出，***【ŷ】****，应该是***，现在是*。*****

 ***那么，如何告诉神经网络它离我们想要的输出有多远呢？进来的是 ***失去功能的*** 前来救援。

* * *

### 损失函数

***损失函数*** 是一个简单的等式，它告诉我们我们的神经网络的预测输出(***【ŷ***)与我们的期望输出( ***y*** )， ***例如，只有*** 。

损失函数的 ***导数*** 指示是否增加或减少权重。正导数意味着减少权重，负导数意味着增加权重。 ***坡度越陡，预测越不准确。***

<figure>

[![](img/f5c3448f5502429126b003d793686862.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kr1KN3RY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3jkzbbd7jyqr74lo4und.png)

<figcaption>Fig 11\. Loss function visualized</figcaption>

</figure>

图 11 所示的损失函数曲线是一个理想的版本。在现实世界的情况下，损失函数可能不会如此平滑，沿途会有一些颠簸和鞍点达到最小值。

有许多不同种类的损失函数 ***，每一个本质上都是计算预测输出和期望输出*** 之间的误差。这里我们将使用一个最简单的损失函数， ***平方误差损失函数*** 。定义如下:

<figure>

[![](img/60f17a248d5cd5c48070b78eec96bc70.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--79bKf3EB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/84djv30dhr6w7vpwjxsf.png)

<figcaption>Fig 12\. Loss Function. Calculating error for a single example</figcaption>

</figure>

取**的平方可以保持一切都是好的和正的，分数(1/2)在那里，以便在对平方项** *求导时抵消(在一些机器学习实践者中，省略分数是很常见的)*。

直观地说，平方误差损失函数帮助我们最小化预测线(蓝线)和实际数据(绿点)之间的垂直距离。在幕后，这条预测线就是我们的 ***z*** (线性函数)节点。

<figure>

[![](img/b5442849c5466b7887f0ecebe4d5b807.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cOkhCg09--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/axy337v232iae90ojm7q.png)

<figcaption>Fig 13\. Visualization of the effect of the Loss function</figcaption>

</figure>

* * *

现在我们知道了损失函数的目的，让我们计算当前预测的误差***【ŷ=0.5***，假设 ***y=0***

<figure>

[![](img/80d2abd47525eeebda70997e2e7f0017.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BnLxCxX4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sd6z3v6ljg4ne9165dg9.png)

<figcaption>Fig 14\. Loss calculated for 1ˢᵗ example</figcaption>

</figure>

我们可以看到损失是 0.125。鉴于此，我们现在可以使用损失函数的导数来检查我们是否需要增加或减少我们的权重。

这个过程被称为 ***反向传播*** ，因为我们将做与正向阶段相反的事情。我们将从输出到输入反向跟踪，而不是从输入到输出。简单地说，反向传播允许我们计算出神经网络的每个部分造成了多少损失。

* * *

为了执行反向传播，我们将采用以下技术:*在每个节点，我们仅计算我们的局部梯度(该节点的偏导数)，然后在反向传播期间，当我们从上游接收梯度的数值时，我们将这些数值与局部梯度相乘，以将它们传递到它们各自连接的节点。*

<figure>

[![](img/0305198a99bcbe5a763354d331802d6a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2CGPl9rs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lg1qm4usalpqidlo20wv.png)

<figcaption>Fig 15\. Gradient Flow</figcaption>

</figure>

这是从微积分中概括出的 ***链式法则*** 。

* * *

由于*(预测标号)决定了我们的 ***损失******【y】***(实际标号)是不变的，对于一个单独的例子，*我们将对***的损失取偏导数***

 ***<figure>

[![](img/c947652c048683b4ac1df28103b48965.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CaLQtGRY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1ayk7soj9f0gwy1u36ec.png)

<figcaption>Fig 16\. The partial derivative of Loss w.r.t ŷ</figcaption>

</figure>

由于反向传播步骤可能看起来有点复杂，我将一步一步地介绍它们:

<figure>

[![](img/9b5ed1627553b01d17a4f1094d699ef9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--khDACEO7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mth4xvo1pd192vtntp2g.png)

<figcaption>Fig 17.a. Backpropagation</figcaption>

</figure>

* * *

对于下一个计算，我们需要 sigmoid 函数的导数，因为它形成了红色节点的局部梯度。让我们推导一下。

<figure>

[![](img/d52157ba3e31e40f091fbec1e6366da0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Qehe82XW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8olcek7z3blol39qc4qm.png)
[![](img/73e488c9daa51f1c6529b367f65f4117.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--duHnqTCC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/v4j7zwks2mdzdv2nrem4.png)
[![](img/721f8484da95977600801b023cac508e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Lo2lF_uJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/10fwknidljvf4ft3f369.png)

<figcaption>Fig 18\. The derivative of the Sigmoid function.</figcaption>

</figure>

让我们在下一次逆向计算中使用它

* * *

<figure>

[![](img/6b174b2ab8491591999aa7298fe1912d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vNXbB0sM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o133o9x62icgeo6285ya.png)

<figcaption>Fig 17.b. Backpropagation</figcaption>

</figure>

反向计算不应一直传播到输入，因为我们不想更改输入数据(即红色箭头不应指向绿色节点)。我们只想改变与输入相关的权重。

<figure>

[![](img/79d21862e73e9ff34232527e44edab91.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1NOyxerM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/taomfov3or7hvnqzzetr.png)

<figcaption>Fig 17.c. Backpropagation</figcaption>

</figure>

注意到奇怪的事情了吗？损失相对于 weights,w₁w₂的导数为零！如果它们的导数为零，我们就不能增加或减少权重。那么，在这种情况下，如果我们不知道如何调整权重，我们如何获得我们想要的输出呢？*这里需要注意的关键点是，局部梯度(***【∂z/∂w₁】***和***【∂z/∂w₂】***)是*和***【x₂】****，在本例中，这两个梯度恰好为零(即不提供任何信息)*

 *这就给我们带来了 ***偏差*** 的概念。

* * *

### 偏差

回忆一下你高中时代的一条线的方程。

<figure>

[![](img/c4686d9f0b835538a2a76a449d38d854.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9ImPmaih--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xqdeukildqe895m8kc4p.png)

<figcaption>Fig 19\. Equation of a Line</figcaption>

</figure>

这里 ***b*** 是偏置项。直观地说，偏差告诉我们，所有用**T5 xT7(*自变量*)计算的输出都应该有一个加性偏差 ***b*** 。所以，当 ***x=0*** (没有来自*自变量的信息)输出应该偏向恰好* ***b*** 。**

*注意，如果没有偏置项，一条直线只能通过原点(0，0 ),那么直线之间唯一的区分因素将是梯度 ***m*** 。*

<figure>

[![](img/88b1bd1b48c58ccc2e0f8b4c6a612146.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GqhlNf32--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d4nnz0oc1dh6y99ve7y0.png)

<figcaption>Fig 20\. Lines from origin</figcaption>

</figure>

* * *

因此，利用这些新信息，让我们给神经网络增加另一个节点；偏置节点。*(在神经网络文献中，除了输入层，每一层都假设有一个偏置节点，就像线性节点一样，所以这个节点在图中也经常被省略。)*

<figure>

[![](img/6122293b329690f8fedab7133e4d086f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--teFOyNN3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/b6plufpnsckqddkbdigk.png)

<figcaption>Fig 21\. Expanded neural network with a bias node</figcaption>

</figure>

现在让我们用同一个例子做一个正向传播，*****y = 0***并且让我们设置 bias， ***b=0*** ( *初始 bias 总是设置为零，而不是一个随机数*)，让 Loss 的反向传播算出 bias。**

 **<figure>

[![](img/21948ab700dee2e6b8a6847ec96eb682.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NAyMGhyV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9qo6hllwukkd0ltnao5i.png)

<figcaption>Fig 22\. Forward propagation of the first example from OR table data with a bias unit</figcaption>

</figure>

好了，偏置为***“b = 0”***的前向传播根本没有改变我们的输出，但是在我们做出最终判断之前，先做一下后向传播。

像以前一样，让我们一步一步地进行反向传播。

<figure>

[![](img/56d15679a7d9c5ee6ba4386ae37146d6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--p3uUzgEY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hbb22sxihz72ww7oary6.png)

<figcaption>Fig 23.a. Backpropagation with bias</figcaption>

</figure>

<figure>

[![](img/fd6724c257a5ca04a7e71f9eef99cab3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cQxud23C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/g2ai773u9lcypjdcb9zh.png)

<figcaption>Fig 23.b. Backpropagation with bias</figcaption>

</figure>

<figure>

[![](img/156386db3cea1ed0a2a898b1463f9515.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xRNtYTQe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rwax3w4cbiubhe3n7blt.png)

<figcaption>Fig 23.c. Backpropagation with bias</figcaption>

</figure>

万岁！我们刚刚算出了调整偏差的幅度。由于偏差的导数(***【∂l/∂b】***)为正 0.125，我们将需要通过向梯度的负方向移动来调整偏差(回想之前的损失函数曲线)。这在技术上被称为 ***梯度下降*** ，因为我们使用梯度的方向从倾斜区域“下降”到平坦区域。就这么办吧。

<figure>

[![](img/a4c1051772b1bfa840506e4be63abc9f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fNx2gcDE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rl0e9orqxecso5kuqhb8.png)

<figcaption>Fig 24\. Calculated new bias using gradient descent</figcaption>

</figure>

现在，我们已经将偏差稍微调整为 ***b=-0.125*** ，让我们通过执行 ***正向传播*** 和 ***检查新损失*** 来测试我们是否做对了。

<figure>

[![](img/2c6d1b1dde1f944d3f4aa47cd1773502.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--a0AvJT7w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9domolr0n2gjthk0tbt8.png)

<figcaption>Fig 25\. Forward propagation with newly calculated bias</figcaption>

</figure>

<figure>

[![](img/6110537e4287a739d13b63333e0f46eb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--s9P-W31R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tflu4ss167ncbsh9j6db.png)

<figcaption>Fig 26\. Loss after newly calculated bias</figcaption>

</figure>

现在我们预测的产量是*(*四舍五入到小数点后三位*)，比之前的 0.5 略有提高，损耗从 0.125 下降到大约 ***0.109*** 。这种轻微的校正是神经网络通过将其预测输出与期望输出 ***y*** 进行比较而“学习”的，然后沿着与梯度相反的方向移动。很酷，对吧？*

 *现在你可能想知道，这仅仅是对之前结果的一个小的改进，我们如何达到最小的损失。两件事开始起作用: ***a)我们执行多少次迭代的‘训练’***(每个训练周期是前向传播，然后是反向传播，并通过梯度下降更新权重) ***b)学习率*** 。

学习率？？？那是什么？我们来谈谈吧

* * *

### 学习率

回想一下，上面我们是如何通过向梯度的相反方向移动(即 ***梯度下降*** )来计算新的偏差的。

<figure>

[![](img/a9917fbd8c00da967c5e57e82b18c8d8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---TvSq0hZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/azz60l4q4lxaa26e96ud.png)

<figcaption>Fig 27\. The equation for updating bias</figcaption>

</figure>

请注意，当我们更新偏差时，我们在梯度的相反方向上移动了 ***1 步。*T3】**

<figure>

[![](img/f5b0598af9c04c43ba0017cf05e59842.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sQsRRc7a--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ifw5hnx1llzukzd120yn.png)

<figcaption>Fig 28\. The equation for updating bias showing "step"</figcaption>

</figure>

我们可以在梯度的相反方向上移动 0.5、0.9、2、3 或任何我们想要的步长分数。这个*‘步数’*就是我们定义的 ***学习率*** ，通常用 ***α*** (alpha)来表示。

<figure>

[![](img/9e378540263dc8a39dfbf8b9f3d2d887.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PG5sqvjC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/45w97es9xnz9mw725hcm.png)

<figcaption>Fig 29\. The general equation for gradient descent</figcaption>

</figure>

学习率定义了我们多快达到最小损失。让我们在下面想象一下学习率的情况:

<figure>

[![](img/e4d7fcebc117717f9ed036df1c68aed1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VPnkERmt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/n2maila4cpxvx3w57j5h.png)

<figcaption>Fig 30\. Visualizing the effect of learning rate.</figcaption>

</figure>

正如你所看到的，学习率越低(α=0.5)，我们沿着曲线下降的速度越慢，我们要走很多步才能到达最低点。另一方面，学习率越高(α=5 ),我们迈出的步伐越大，到达最小值的速度也越快。

眼尖的人可能已经注意到，当我们越来越接近最小值时，梯度下降步骤(绿色箭头)变得越来越小，这是为什么呢？回想一下，学习率正乘以曲线上该点的梯度；当我们从倾斜区域下降到 u 形曲线的平坦区域时，在最小点附近，梯度变得越来越小，因此台阶也变得越来越小。因此，在训练期间改变学习率是不必要的(梯度下降的一些变化以高学习率开始沿斜坡快速下降，然后逐渐降低，这被称为“使学习率退火”)

那么外卖是什么？只要把学习率设置的尽可能的高，快速达到最优损耗就可以了。不会。学习率可能是一把双刃剑。过高的学习率和参数(权重/偏差)不会达到最优，而是开始偏离最优。学习率过小，参数收敛到最优值的时间过长。

<figure>

[![](img/d9feb8f11e3840d58d59befd47d0bd71.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CzIwvIHA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/iol0nbqlpr42ms0im189.png)

<figcaption>Fig 31\. Visualizing the effect of very low vs. very high learning rate.</figcaption>

</figure>

小学 rate(α=5*10⁻ ⁰)由此而来的是无数的步骤，达到最低点是不言自明的；将梯度乘以一个小数值(α)会产生一个成比例的小步长。

导致梯度下降发散的大学习率(α=50)可能是混杂的，但答案相当简单；请注意，在每一步，梯度下降通过直线移动(图中的绿色箭头)来近似其向下的路径，简而言之，它估计其向下的路径。当学习速率太高时，我们强制梯度下降以采取更大的步长。较大的步长倾向于过高估计向下的路径，并越过最小值点，然后为了校正不好的估计，梯度下降试图向最小值点移动，但是由于较大的学习速率，再次越过最小值。这种连续高估的循环最终导致结果出现偏差(每次训练循环后损失增加，而不是减少)。

学习率就是所谓的*。超参数是神经网络基本上不能通过梯度的反向传播来学习的参数，它们必须由神经网络模型的创建者根据问题及其数据集来手动调整。*(上面损失函数的选择也是超参数)**

 *简而言之，目标不是找到“完美的学习率”,而是一个足够大的学习率，以便神经网络成功有效地训练而不发散。

* * *

到目前为止，我们只使用了一个例子( ***x₁=0*** 和 ***x₂=0*** )来调整我们的权重和偏差(*实际上，到目前为止只有我们的偏差*🙃)这减少了我们整个数据集(或选通表)中一个例子的损失。但我们有不止一个例子可以借鉴，我们希望减少所有例子中的损失。 ***理想情况下，在一次训练迭代中，我们希望减少所有训练样本的损失。*** 这被称为 ***批量梯度下降*** (或全批量梯度下降)，因为我们在每次训练迭代中使用整批训练样本来提高我们的权重和偏差。*(其他形式有 ***小批量梯度下降*** ，其中我们在每次迭代中使用数据集的子集，以及 ***随机梯度下降*** ，其中我们到目前为止在每次训练迭代中仅使用一个示例)*。

*神经网络遍历所有训练样本的训练迭代被称为 ***时期*** 。如果使用小批量，则在神经网络遍历所有小批量之后，一个时期将是完整的，类似于随机梯度下降，其中批量只是一个例子。*

在我们继续之前，我们需要定义一个叫做 ***的成本函数*** 。

* * *

### 成本函数

当我们执行*“批次梯度下降”*时，我们需要稍微改变损失函数，以适应批次中的所有示例，而不仅仅是一个示例。这个调整后的损失函数被称为 ***成本函数*** 。

*另外，注意成本函数的曲线类似于损失函数的曲线(同样的 U 型)。*

成本函数不是计算一个示例的损失，而是计算所有示例的平均损失。T3】

<figure>

[![](img/e7c8734519fe5ee305ea2885580eb2ae.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--w1cdO5Tj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/l3xxpazqx73u2fvqttwn.png)

<figcaption>Fig 32\. Cost function</figcaption>

</figure>

直观上，成本函数扩展了损失函数的能力。回想一下，损失函数是如何帮助最小化一个*单个*数据点和预测线(*)之间的垂直距离的。**成本函数有助于同时最小化多个数据点之间的垂直距离(平方误差损失)。***

 *<figure>

[![](img/7ac5eefe2cc5398a758fd31a06d1ab82.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Tmp67gfu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/knqchtiau983lgpshu6z.png)

<figcaption>Fig 33\. Visualization of the effect of the Cost function</figcaption>

</figure>

***在批量梯度下降期间，我们将使用成本函数的导数，而不是损失函数*** ，来引导我们在所有示例中达到最小成本的路径。*(在一些神经网络文献中，成本函数有时也用字母***【J】***来表示)。)*

让我们看看成本函数的导数方程与损失函数的普通导数有什么不同。

#### 成本函数的导数

<figure>

[![](img/0ccd1e0e19c6f0cd9a89e9f2461ed594.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jwk3jgMl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ezjopewoucgicbu7hoco.png)

<figcaption>Fig 34\. Cost function showing it takes input vectors</figcaption>

</figure>

对这个成本函数求导可能有点冒险，成本函数将向量作为输入并求和。所以，在推广导数之前，让我们从一个简单的例子开始。

<figure>

[![](img/a1c2dbf3be324fab752203d2956030fe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--34FlZ7cz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6wpn2i6ncdn6ejva7dnk.png)

<figcaption>Fig 35\. Calculation of Cost on a simple vectorized example</figcaption>

</figure>

在成本计算方面没有什么新的东西。正如预期的那样，最终的成本是损失的平均值，但现在实现是矢量化的*(我们执行矢量化减法，然后执行元素幂运算，称为 Hadamard 幂运算)*。让我们推导偏导数。

<figure>

[![](img/be44878dbc21f77afdcb7660ad1d83b6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--27NzC_VN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/oj03ytj8r3h3srnmwnf0.png)

<figcaption>Fig 36\. Calculation of Jacobian on the simple example</figcaption>

</figure>

由此，我们可以推广偏导数方程。

<figure>

[![](img/cf1177a7d2068986978dec2ac603d266.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XZcURcOO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/z9a6bjcisydp9j99625p.png)

<figcaption>Fig 37\. Generalized partial derivative equation</figcaption>

</figure>

现在我们应该花点时间来注意，损失的导数，和成本的导数是如何不同的。

<figure>

[![](img/ac60a0161d3b9923770f879e211bc84f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hf-a-JZ_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d8cx33jnf2n759bdskja.png)

<figcaption>Fig 38\. Comparison between the partial derivative of Loss and Cost with respect to(w.r.t) **ŷ⁽ⁱ⁾**</figcaption>

</figure>

我们以后会看到这个小变化是如何在梯度的计算中表现出来的。

* * *

回到批量梯度下降。

1.  对于每个训练迭代，创建单独的临时变量(大写 deltas，δ),这些变量将累积来自我们训练集中的每个***【m】***示例的权重和偏差的梯度(小写 deltas，δ),然后在迭代结束时使用累积梯度的平均值更新权重。这是一个缓慢的方法。*(对于那些熟悉的时间复杂度分析，你可能会注意到，随着训练数据集的增长，这成为一个多项式时间算法，O(n ))*

<figure>

[![](img/4bc45d9a9f1b699872723d7d2dc2a461.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ojBsG-M7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/w1qdgm3e5krbeq6pl5oa.png)

<figcaption>Fig 39\. Batch Gradient Descent slow method</figcaption>

</figure>

1.  更快的方法与上面类似，但是使用矢量化计算来一次性计算所有训练示例的所有梯度，因此去除了内部循环。矢量化计算在计算机上运行得更快。这是所有流行的神经网络框架所采用的方法，也是我们将在本博客的其余部分遵循的方法。

对于矢量化计算，我们将对神经网络计算图的“Z”节点进行调整，并使用成本函数代替损失函数。

<figure>

[![](img/164c8f719c496802457b0eb95a5835dd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--05zDiYx8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8lzmdjhjjwxi239qh5yg.png)

<figcaption>Fig 40\. Vectorized implementation of Z node</figcaption>

</figure>

注意，在上图中，我们在*和*之间取*点积，它可以是适当大小的矩阵或向量。偏差， ***b*** ，在这里仍然是一个单一的数字(*一个标量*)，并且将以元素方式添加到点积的输出中。预测的输出将不仅仅是一个数字，而是一个向量，*，其中每个元素都是它们各自示例的预测输出。****

 ***在进行前向和后向传播之前，让我们设置输出数据( ***X，W，b & Y*** )。

<figure>

[![](img/efe62de52c3337bc901a10856019324f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fiofkK1b--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/n2xvemilggldoc7xt6ok.png)

<figcaption>Fig 41\. Setup data for vectorized computations.</figcaption>

</figure>

我们现在终于准备好使用***【xₜᵣₐᵢₙ】******【yₜᵣₐᵢₙ】******w***和 ***b*** 来执行正向和反向传播。

*(注:为简洁起见，以下所有结果均四舍五入至小数点后 3 位)*

<figure>

[![](img/11fc7a97596a72e51481051a57b5a2ef.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--L6obeqcP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bmug1olcjmo1aa5jp3bn.png)
[![](img/a776ca558c7bf4e70aa5a1ccd6f3d079.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--prAbTrLG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7815coqj7o3gxw4wfqee.png)

<figcaption>Fig 42\. Vectorized Forward Propagation on OR gate dataset</figcaption>

</figure>

多酷啊，我们一次就计算出了数据集中所有例子的所有正向传播步骤，仅仅是通过对我们的计算进行矢量化。

我们现在可以根据这些产量预测计算出 ***成本*** 。*(我们将详细检查计算，以确保没有混淆)*

<figure>

[![](img/78863e83291a9e407042ff9efefa5a0b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--G0NFabsN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ai89pw3gflutjr0p0lfq.png)

<figcaption>Fig 43\. Calculation of Cost on the OR gate data</figcaption>

</figure>

我们的**成本**与我们当前的权重 **W** ，结果是 **0.089** 。我们现在的目标是使用反向传播和梯度下降来降低这个成本。和以前一样，我们将一步一步地进行反向传播

<figure>

[![](img/b4d04dbf900c2e66f71dcd433edc6259.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RmbktSuk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0u62cfib25a4cwtj46a0.png)
[![](img/d48c4fb21b059cd1f5e93af96df118ba.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--1vWF9uPz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fgaouo6412gxl2xc2n5v.png)

<figcaption>Fig 44.a. Vectorized Backward on OR gate data</figcaption>

</figure>

<figure>

[![](img/8740e4f5274ca58d5c3065af31a552ea.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--l3-0q7yw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cj7hg0tj9fc6d3po6t0c.png)
[![](img/c8973fb847cf1f4cdff81463917376d0.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--zepHBOka--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ojrtthit2k2ut653uwok.png)

<figcaption>Fig 44.b. Vectorized Backward on OR gate data</figcaption>

</figure>

<figure>

[![](img/7431df351bc4903eb733205daf8e2d66.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--G1SdZK7o--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xbfwwcdvnnffqbtjjdcy.png)
[![](img/727c0ebbd12e7ec4209fe1ea307bdd3f.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--QFG_Z6P3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lpo1q8mmo5t3gy7lv8by.png)

<figcaption>Fig 44.c. Vectorized Backward on OR gate data</figcaption>

</figure>

瞧，我们使用批量梯度下降的矢量化实现来一次性计算所有梯度。

(眼尖的人可能想知道在这最后一步中，局部梯度和最终梯度是如何计算的。别担心，我会在最后一步解释梯度的推导，很快。现在，可以说最后一步定义的梯度是对计算∂Cost/∂W 和∂Cost/∂b 的简单方法的优化

让我们更新权重和偏差，保持学习速率与之前的非矢量化实现相同，即 ***α=1*** 。

<figure>

[![](img/40444a30d06481fb8e7b5d1afad8c110.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7jhcYp3g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pytf2zdsdpicrhkxa4qw.png)

<figcaption>Fig 45\. Calculated new Weights and Bias</figcaption>

</figure>

现在我们已经更新了权重和偏差，让我们做一个**前向传播**和**计算新的成本**来检查我们是否做了正确的事情。

<figure>

[![](img/e278b92868867ecbf9896c3137c00830.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oOJwVN-q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lbxnno5sqlcs9gtpkjdi.png)
[![](img/c948ee4167cf6d2e038721e2a77b0185.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--1OMFTy_j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m59aza9klkwp9buolzi3.png)

<figcaption>Fig 46\. Vectorized Forward Propagation with updated weights and bias</figcaption>

</figure>

<figure>

[![](img/c98737dcbca107053d9769395b2fc829.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Q9XquPK4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6u3rjqcqtxqr9ajjcu9f.png)

<figcaption>Fig 47\. New Cost after updated parameters</figcaption>

</figure>

因此，我们*将我们的成本(所有示例的平均损失)*从大约为*0.089 的初始成本降低到 ***0.084*** 。在我们能够收敛到低成本之前，我们将需要进行多次训练迭代。*

 *在这一点上，我建议您自己执行反向传播步骤。那结果应该是(四舍五入到小数点后 3 位): ***∂Cost/∂W = [-0.044，-0.035]******∂cost/∂b =[-0.031]。***

回想一下，在我们训练神经网络之前，我们如何预测神经网络可以在图 9 中分离这两个类，在大约 5000 个时期(完全批量训练迭代)之后，成本稳定地降低到大约 ***0.0005*** ，并且我们得到以下决策边界:

<figure>

[![](img/7716909dbe57d6db2371fffd8d86f96c.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--nvy6tcv5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nri2nqzowvqe7x6e9que.png) [ ![](img/8ec88e3639102c8a1e13ff507e15216a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--i5wh_Dxx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3vn3odi9qeyjbf06j1pl.png)

<figcaption>Fig 48\. Cost curve and Decision boundary after 5000 epochs</figcaption>

</figure>

**成本曲线**基本上是在一定数量的迭代(时期)后绘制的成本值。请注意，成本曲线在大约 3000 个时期后变平，这意味着神经网络的权重和偏差已经收敛，因此进一步的训练只会稍微改善我们的权重和偏差。为什么？回想一下 u 形损失曲线，当我们下降越来越接近最小点(平坦区域)时，梯度变得越来越小，因此梯度下降的步长非常小。

**决策边界**显示了神经网络的决策从一个输出变为另一个输出的路线。我们可以通过给决策边界上下的区域着色来更好地形象化这一点。

<figure>

[![](img/900154a1b2ee7e41bb7c144588855130.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tk-TpiBE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9johty29zmet36qav1dd.png)

<figcaption>Fig 49\. Decision boundary visualized after 5000 epochs</figcaption>

</figure>

这就清楚多了。红色阴影区域是决策边界以下的区域，决策边界以下的所有内容都有一个 ***0*** 的输出(***【ŷ***)。类似地，决策边界以上的所有内容(绿色阴影)的输出为 ***1*** 。总之，我们的简单神经网络通过查看训练数据并找出如何分离其两个输出类( ***y=1*** 和 ***y=0*** )来学习决策边界🙌。现在输出神经元活跃起来🔥(产生 1)每当 ***x₁*** 或 ***x₂*** 或两者都为 1。

现在是查看成本函数中的***【1/m】***(***【m】***是训练数据集中示例的总数)如何在梯度的最终计算中表现出来的好时机。

<figure>

[![](img/faa2676fb14f0245e7e5d8266edd0642.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7CCiPOOA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kkikpzlhyz94vd343oua.png)

<figcaption>Fig 50\. Comparing the effect of derivative w.r.t Cost and Loss on parameters of the neural network</figcaption>

</figure>

***由此可知，最重要的一点是，使用代价函数来更新我们的权重所使用的梯度是在一次训练迭代期间计算的所有梯度的平均值；同样适用于偏置*的**。您可能想通过检查矢量化计算来确认这一点。

取所有梯度的平均值有一些好处。首先，它给我们一个较少噪声的梯度估计。第二，得到的学习曲线是平滑的，帮助我们容易地确定神经网络是否在学习。当在更复杂的数据集上训练神经网络时，例如那些错误标记的例子，这两个特征都非常方便。

* * *

### 这很好，但是你是如何计算∂Cost/∂W 和∂Cost/∂b 的梯度的呢？🤔

我学习的神经网络指南和博客帖子经常会省略复杂的细节，或者给出非常模糊的解释。不是在这个博客里，我们会检查每一件事，不遗余力。

#### 首先，我们来看看∂Cost/∂b.为什么要对梯度求和？

为了解释这一点，我在三个非常简单的方程上使用了我们的计算图形技术。

<figure>

[![](img/d268c30d5126dfe8eb687fdc711c9bd6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3nqgMz3z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4g3zdnbrdbxubu4atf9i.png)

<figcaption>Fig 51\. Computational graph of simple equations</figcaption>

</figure>

我对 **b** 节点特别感兴趣，我们就此做一下反向传播吧。

<figure>

[![](img/bef697dc044f0372d29bb14afd864f92.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uyljAsv1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9zrosvtd12heblinx6j5.png)

<figcaption>Fig 52\. Backpropagation on the computational graph of simple equations</figcaption>

</figure>

注意， ***b*** 节点正在从**两个**其他节点接收梯度。因此，流入节点 ***b*** 的渐变总数是流入的两个渐变的总和。

<figure>

[![](img/4fd63b94e9ef24d2e322acae00ca1cce.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--M6yomCK_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7f9e3vsv1o4la3c1tppq.png)

<figcaption>Fig 53\. Sum of gradients flowing into node **b**</figcaption>

</figure>

从这个例子中，我们可以归纳出以下规则: ***将所有进入的渐变求和到一个节点，从所有可能的路径*** 。

让我们想象一下这个规则是如何用于计算*偏差的。我们的神经网络可以被视为对我们的每个示例进行 ***独立*** 计算，但是在训练迭代期间使用权重和偏差的共享参数。Below bias( ***b*** )被可视化为我们的神经网络执行的所有单独计算的共享参数。*

 *<figure>

[![](img/91d46b0c44a8e30fb828187532e9a735.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7_OLVZ43--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6qqk6qfj6ewovmzrxejh.png)

<figcaption>Fig 54\. Visualizing bias parameter being shared across a training epoch.</figcaption>

</figure>

遵循上面定义的一般规则，我们将对从所有可能路径到偏置节点的所有输入梯度求和， **b** 。

<figure>

[![](img/59350378deb9305f91fdc8ebf206b6f9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yIb8On3V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0u1ya8fnz6t6wlfxh1li.png)

<figcaption>Fig 55\. Visualizing all possible backpropagation paths to shared bias parameter</figcaption>

</figure>

因为∂z/∂b(z 节点处的局部梯度)等于 **1** ，所以 ***b*** 处的总梯度是来自每个示例的关于成本的梯度的总和。

<figure>

[![](img/e632ad0bc20b54d1fe842dd2df8a9eda.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SGY5aRKb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vwz30dj8oxrflcae1xhy.png)

<figcaption>Fig 56\. Proof that ∂Cost/∂b is the sum of upstream gradients</figcaption>

</figure>

既然我们已经得到了偏差的导数，让我们继续讨论权重的导数，更重要的是权重的局部梯度。

#### 局部 gradient(∂Z/∂W)如何等于输入训练数据的转置(X_train)？

这可以用与上述偏差计算类似的方式来回答，但是这里的主要复杂性是计算权重矩阵( **W** )和数据矩阵( **Xₜᵣₐᵢₙ** )之间的点积的导数，这形成了我们的局部梯度。

<figure>

[![](img/28030dbcf0d53ff89001ba2dd85e7508.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pKDrYrjq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ij1oeugf50snn84fc6ia.png)

<figcaption>Fig 57.a. Figuring out the derivative of the dot product.</figcaption>

</figure>

点积的这个导数有点复杂，因为**我们不再处理标量**，相反， **W** 和 **X** 都是矩阵，而 **W⋅X** 的结果也是矩阵。让我们先用一个简单的例子来更深入一点，然后再从中归纳。

<figure>

[![](img/2cc968302bbc7846d0ef3fff4dc98405.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nVSwrLuG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6oy72r4rmund6367n753.png)

<figcaption>Fig 57.b. Figuring out the derivative of the dot product.</figcaption>

</figure>

让我们计算一下 **A** 相对于 **W** 的导数。

<figure>

[![](img/16c1f25e2a70cfb2617f85ef54d4f966.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5wXdmM8s--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uqte14ax6ijqmzqzrbvk.png)

<figcaption>Fig 57.c. Figuring out the derivative of the dot product.</figcaption>

</figure>

让我们在同时处理多个例子的训练迭代的情况下想象一下。*(注意输入的例子是列向量。)*

<figure>

[![](img/bd99794289db7036a17aaf3d6129c836.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AssMjboG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vr8meel3pxclla57ei73.png)

<figcaption>Fig 58\. Visualizing weights being shared across a training epoch</figcaption>

</figure>

正如偏差( **b** )在训练迭代中的每个计算中被共享一样，权重( **W** )也被共享。我们还可以看到梯度流回权重，如下所示*(注意，每个示例的局部导数 w.r.t 到 **W** 产生输入示例的行向量，即输入的转置)*:

<figure>

[![](img/48a86e13a5ea7a66c25aa0801ad7f838.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--i-mmLTAA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/e9e97f7xfnc7yfgupoex.png)

<figcaption>Fig 59\. Visualizing all possible backpropagation paths to shared weights parameter</figcaption>

</figure>

同样，遵循上面定义的一般规则，我们将对从所有可能路径到权重节点的所有输入梯度求和， **W** 。

<figure>

[![](img/f49e8061ea66d6acfe0b47cc91fc4f82.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rge9eb-c--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5uh87cryy1ptdomegw77.png)

<figcaption>Fig 60\. Derivation of ∂Cost/∂W after visualization.</figcaption>

</figure>

到目前为止，我们所做的计算，*，虽然是正确的，并且是一个很好的解释，但是，这不是一个优化的计算。我们也可以将这种计算矢量化。让我们接下来做那个*

 *<figure>

[![](img/50f2fadfd389fa798c1a4c35c3444f4c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yylQEZlS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x4mgz2oq8yfdvuz22083.png)

<figcaption>Fig 61\. Proof that ∂Cost/∂W is the dot product between the Upstream gradient and the transpose of **Xₜᵣₐᵢₙ**</figcaption>

</figure>

#### 有没有更简单的方法来解决这个问题，而不需要数学？

**是的！使用*量纲分析*。**

在我们的 OR 门示例中，我们知道流入节点***【z】***的梯度是一个(1 × 4)矩阵，**是一个(2 × 4)矩阵，成本相对于 **W** 的导数需要与 **W** 的大小相同，即(1 × 2)。因此，生成(1 × 2)矩阵的唯一方法是取 z 和 Xₜᵣₐᵢₙ的转置之间的点积。**

 **[![](img/2edd13eda3cf8a12b914889ba481369f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qoJ9tJGq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qmjgqzvq9wdcqhyfys1y.png)

同样，已知 bias， ***b*** ，是一个简单的(1 × 1)矩阵，流入节点 Z 的梯度是(1 × 4)，使用量纲分析我们可以确定成本 w.r.t ***b*** 的梯度也需要是一个(1 × 1)矩阵。我们能做到这一点的唯一方法，给定局部梯度(***【∂z/∂b】***)正好等于 ***1*** )，就是对上游梯度求和。

最后一点，推导导数表达式时，先做一些小例子，然后从这些例子中进行归纳。例如，在这里，当计算点积 w.r.t 对 **W** 的导数时，我们使用单个列向量作为测试用例，并从那里进行推广，如果我们使用整个数据矩阵，那么导数将产生一个(4 × 1 × 2)张量(多维矩阵)，对它的计算可能有点麻烦。

* * *

在结束本节之前，让我们看一个稍微复杂一点的例子。

<figure>

[![](img/ef512b15b9e20ec10e1580d89ed8c30a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WqzmTFcY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o9dv23fqtyanhvi1tuvx.png)

<figcaption>Fig 62\. XOR gate data</figcaption>

</figure>

上面的图 62 显示了一个异或门数据。看着它注意，只有当值*或 ***x₂*** 中的一个等于 ***1*** 时，标签 **y** 才等于 ***1*** ，而不是两个都等于。这使得它成为一个特别具有挑战性的数据集，因为数据不是线性可分的，即没有一个单一的直线判定边界可以成功地将数据中的两个类( ***y=1 和 y=0*** )分开。XOR 曾经是早期人工神经网络的祸根。*

 *<figure>

[![](img/23ac2b31f7225c60f9177e0a9fed2952.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--B3PR3nLr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/69ghath4q1qr2bcl8jh1.png)

<figcaption>Fig 63\. Some linear decision boundaries that are wrong</figcaption>

</figure>

回想一下，我们当前的神经网络之所以成功，只是因为它能够计算出能够成功分离两类或门数据集的直线决策边界。直线不会在这里切断它。那么，我们如何让神经网络来解决这个问题呢？

嗯，我们可以做两件事:

1.  修改数据本身，以便除了特征*和*之外，第三个特征提供一些附加信息来帮助神经网络决定好的决策边界。这个过程称为 ***特征工程*** 。**

***   改变神经网络的架构，使其更深入。** 

 **让我们看一看哪一个更好。

#### 特征工程

让我们看一个类似于 XOR 数据的数据集，它将帮助我们实现一个重要的认识。

<figure>

[![](img/b8b8f0ea4033cb74ba26f20acccd4edf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fPJ6sLZl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jcee7vqvetvxpcpkyjto.png)

<figcaption>Fig 64\. XOR-like data in different quadrants</figcaption>

</figure>

图 64 中的数据与 XOR 数据完全一样，只是每个数据点分布在不同的象限。请注意，在 **1ˢᵗ和 3ʳᵈ象限中，所有值都是正的**，而在 **2ⁿᵈ和 4ᵗʰ象限中，所有值都是负的。**

<figure>

[![](img/0519c1bcb187ce26665bc8ec14464fc4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zjEaYvSC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/z5ko6wyk9ud59dw6sm5z.png)

<figcaption>Fig 65\. Positive and negative quadrants</figcaption>

</figure>

这是为什么呢？在 **1ˢᵗ** 和 **3ʳᵈ** 象限**中，值的符号被平方**，而在 **2ⁿᵈ** 和 **4ᵗʰ** 象限**中，值是负数和正数之间的简单乘积，从而产生负数。**

<figure>

[![](img/a992029c1cd60a124066c379bbae587e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eC6IcziR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c74z42vcutr7lzbini1r.png)

<figcaption>Fig 66\. Result of the product of features</figcaption>

</figure>

所以这给了我们一个使用两个特征的乘积的模式。我们甚至可以在 XOR 数据中看到类似的模式，其中每个象限都可以以类似的方式识别。

<figure>

[![](img/8ad0d99d808fdb4c33f57da1254ce340.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SfH4fNPJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zvxsthsxr5exp5xu6njn.png)

<figcaption>Fig 67\. Quadrant-pattern in XOR data plot</figcaption>

</figure>

***因此，一个好的第三个特征，x₃，将是特征 x₁和 x₂(i.e. x₁*x₂).的乘积*T3】**

*特征的乘积称为一个 ***特征的交叉*** 并产生一个新的 ***合成特征*** 。特征交叉可以是特征本身(如***【x₁】***、 ***x₁*** 、…)，也可以是两个或多个特征的乘积(如***【x₁*x₂***、*、…)，甚至是两者的组合(如 ***x₁ *x₂*** )。例如，在房屋数据集中，输入要素是房屋的宽度和长度(以码为单位),标注是房屋在地图上的位置，此位置的更好预测因子可能是房屋宽度和长度之间的要素交叉，这为我们提供了一个新的“房屋大小(以平方码为单位)”的要素。**

 *让我们将新的合成特征添加到我们的训练数据中，Xₜᵣₐᵢₙ.

<figure>

[![](img/bd19933741b461439c14e6bfe8515844.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---wz-xRum--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/f2q3ybhq5r0moo9p0qfe.png)

<figcaption>Fig 68\. New training data</figcaption>

</figure>

使用这种特征交叉，我们现在可以成功地学习决策边界，而无需显著改变神经网络的架构。我们只需要为 ***x₃*** 添加一个输入节点，并给输入层添加一个对应的权重(随机设置为 0.2)。

<figure>

[![](img/295ab2a92e1f55ef5f12e4670660494c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cv6sztjD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/610pm2y4ylq2vuoe9y74.png)

<figcaption>Fig 69\. Neural Network with feature cross(x₃) as input</figcaption>

</figure>

<figure>

[![](img/4e3bade73f17a04b83bd8c1fcf82b876.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wRwojoqB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pffm0zwgiralojmrcfhn.png)

<figcaption>Fig 70\. Expanded neural network with feature cross(x₃) as input</figcaption>

</figure>

下面给出的是神经网络的第一次训练迭代，你可以自己进行计算并确认它们，因为它们是一个很好的练习。因为我们已经熟悉了这种神经网络架构，所以我不会像以前那样一步一步地进行所有的计算。

*(以下所有计算均四舍五入至小数点后 3 位)*

<figure>

[![](img/302b0f4680ce17b90a8a16469165d208.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--D9rgbWEH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qxki2xg7uhwxn7bw7r46.png)

<figcaption>Fig 71\. Forward Propagation in the first training iteration</figcaption>

</figure>

<figure>

[![](img/6faf2d64988d9f41d02d8cb54410f070.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gJnY7gii--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cd2hqd5ja0fx7q5n1nwl.png)

<figcaption>Fig 72\. Backpropagation in the first training iteration</figcaption>

</figure>

<figure>

[![](img/8756076ccfeb4dfab95e1b4942546875.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KH3VcWr3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/f5bwj5qoxdckxtxfkpyd.png)

<figcaption>Fig 73\. Gradient descent update for new weights and bias, in the first training iteration</figcaption>

</figure>

经过 5000 个时期后，学习曲线和决策边界看起来如下:

<figure>

[![](img/219484a543994633c2c15f31a954df7f.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--UpvVbRba--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k7htutjd23xswvchv678.png) [ ![](img/6ae164834b33030ff902e674252c0918.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nY0dVCZt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vh67nhk1xwm6grt7fhtv.png)

<figcaption>Fig 74\. Learning Curve and Decision Boundary of the neural net with a feature cross</figcaption>

</figure>

和以前一样，为了更好地可视化，我们可以对神经网络的决策从一个变化到另一个的区域进行着色。

<figure>

[![](img/816df06e0d491aba7a4b3c770257b918.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ovf9Wv6v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m1xw4ldpp7xmjpeqzxpm.png)

<figcaption>Fig 75\. Shaded Decision Boundary for better visualization</figcaption>

</figure>

***注意，特征工程允许我们创建一个非线性的决策边界*** 。它是怎么做到的？我们只需要看看 Z 节点在计算什么函数。

<figure>

[![](img/580f1870be160627059977cce82ade8e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uai7L9VL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m8zshj3sf6162l3k2n5z.png)

<figcaption>Fig 76\. Node **Z** is computing a polynomial after adding a feature cross</figcaption>

</figure>

因此，特征交叉帮助我们创建复杂的非线性决策边界。

***这是一个非常强大的想法！*T3】**

#### 改变神经网络架构

这是更有趣的方法，因为它允许我们自己绕过特征工程，而 ***让神经网络计算出特征本身！*T3】**

我们来看看下面这个神经网络:

<figure>

[![](img/22f3c72342730d74e1b52936af976db4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JlBeqhIk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3sj3x1m81j7tb37c27q6.png)

<figcaption>Fig 77\. Neural network with one hidden layer.</figcaption>

</figure>

因此，我们在“或”门示例的神经网络架构中间添加了一些新节点，保持输入层和输出层相同。这一列新节点中间称为一个*隐藏层。*为什么隐藏层？因为在定义它之后，我们不能直接控制隐藏层中的神经元如何学习，不像输入和输出层，我们可以通过改变数据来改变它；此外，由于隐藏层既不构成神经网络的输出也不构成神经网络的输入，所以它们本质上对用户是隐藏的。**

 ****我们可以有任意数量的隐藏层，每层有任意数量的神经元*** 。这种结构需要由神经网络的创建者来定义。因此， ***隐藏层的数量和每层中神经元的数量也是超参数。我们添加的隐藏层越多，我们的神经网络架构就变得越深，我们在隐藏层中添加的神经元越多，网络架构就变得越宽。神经网络模型的深度就是“深度学习”这个术语的来源。***

*经过一些实验，选择了图 77 中具有一个三个乙状结肠神经元隐藏层的架构。*

由于这是一个新的架构，我将一步一步地进行计算。

首先，让我们扩展神经网络。

<figure>

[![](img/6247dfadbedd9b3b2da98f1fb13cbdaa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bXSudwQr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c801iua9u2ve3wdpfgo5.png)

<figcaption>Fig 78\. Expanded neural network with one hidden layer</figcaption>

</figure>

现在让我们执行一个 ***正向传播*** :

<figure>

[![](img/9eebe610a5c3dd8eea7dcaab41dbbe41.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gVUBqMMu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hr2dmof6tk10uc663irw.png)
[![](img/500c79cf8b59674d242e401409a154e2.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--F8p7IWA9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mj8entthrt990jfq8b9o.png)

<figcaption>Fig 79.a. Forward propagation on the neural net with a hidden layer</figcaption>

</figure>

<figure>

[![](img/291973710badd4e6c8b90589120de601.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---82MDie4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7yl0py90n09zkcjvrs7z.png)
[![](img/84ffa5e9fa605b8808e813b400697cb5.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--e79ommTn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/075awbquv4wq1agsbczg.png)

<figcaption>Fig 79.b. Forward propagation on the neural net with a hidden layer</figcaption>

</figure>

我们现在可以计算成本:

<figure>

[![](img/b9e96ea3d77e56a18f6edc142966dac6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DmoFwREc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fo1esaqqki6pq738xzjj.png)

<figcaption>Fig 80\. Cost of the neural net with one hidden layer after **first** forward propagation</figcaption>

</figure>

计算完成本后，我们现在可以进行反向传播，并改进权重和偏差。

<figure>

[![](img/f3e340ebb9ea1a011da35a08b1db49cb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ysTHg3RM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2ih9ds5f0qin7sfjr4gi.png)
[![](img/221a936bdc13d2eee14c44b293c0410d.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--RDnt6DYf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ty23cskpoj4uitjcw4z0.png)

<figcaption>Fig 81.a. Backpropagation on the neural net with a hidden layer</figcaption>

</figure>

<figure>

[![](img/349d2907bd72a33d6da65e96080c3740.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8Nd3SL7Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/08bwow5gaom42d4ayted.png)
[![](img/90562d5afab5735255a474e36539bde8.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--t22vij4T--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4xw5oji7teytfmdq5a98.png)

<figcaption>Fig 81.b. Backpropagation on the neural net with a hidden layer</figcaption>

</figure>

<figure>

[![](img/05fd1a8911111d08e6ce9cb9640e3961.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mAdDVM93--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6qfvcrs711fup36kmyz8.png)
[![](img/4ac6df2e2f6b7f9e822dcfbac4619b55.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--vhy4oVsi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/myks2lqeqcz3oppg8sfs.png)

<figcaption>Fig 81.c. Backpropagation on the neural net with a hidden layer</figcaption>

</figure>

<figure>

[![](img/405e79a400da315655e9bf5b7a04cf5a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lZBt0gcI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t5v582lt4ixkn6kdi5tu.png)
[![](img/3156359647788c53566b2cf7499c7edc.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--Om3YkWSp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/810p7xveqoqzno78u6oi.png)

<figcaption>Fig 81.d. Backpropagation on the neural net with a hidden layer</figcaption>

</figure>

<figure>

[![](img/62eddcc86d677d0bdcfdbc4bd453cbe9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--456d-zEU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tpm8dglq3ukbp2lmc7dh.png)
[![](img/4d29e061c45c2d9aacad849e0a1a0c05.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--qdgR9BNk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9s3qqtf036pcrn96xx6s.png)

<figcaption>Fig 81.e. Backpropagation on the neural net with a hidden layer</figcaption>

</figure>

嚄😅！这是很多，但它大大提高了我们的理解。让我们执行梯度下降更新:

<figure>

[![](img/ecbec08cb8d8bbb9df6a164aae63d0df.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ApUxOqPF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0gexchdyz9ubd95bfu0g.png)

<figcaption>Fig 82\. Gradient descent update for the neural net with a hidden layer</figcaption>

</figure>

在这一点上，我鼓励所有读者自己执行一次训练迭代。产生的梯度应该约为(四舍五入到小数点后 3 位):

<figure>

[![](img/6f337268223d316737cdace98308367b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--m1IAjL7L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/98tjcf89jbvnm8qosn2u.png)

<figcaption>Fig 83\. Derivatives computed during 2ⁿᵈ training iteration</figcaption>

</figure>

在 5000 个历元之后，成本稳步下降到大约 ***0.0009*** ，并且我们得到以下学习曲线和决策边界:

<figure>

[![](img/f0aba0136efced34db23949b74c4d9ed.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--YrWCPEUa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mzh6jfpy4dymea5ioeur.png) [ ![](img/357db13db8977018eeaf6a7f5c9c58f9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sujZ3-2r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kg0ulphl97xuha0xgo9v.png)

<figcaption>Fig 84\. Learning Curve and Decision boundary of the neural net with one hidden layer</figcaption>

</figure>

让我们也想象一下神经网络的决策从 0(红色)变为 1(绿色)的位置:

<figure>

[![](img/1226dca39fdd4b600eeba559d5d751b0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AIVi6OO9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qa2yemvcfqxvplk3wgr9.png)

<figcaption>Fig 85\. Shaded decision boundary of the neural net with one hidden layer</figcaption>

</figure>

这表明神经网络实际上已经学会了在哪里启动(输出 1)和在哪里休眠(输出 0)。

如果我们添加另一个可能有 2 或 3 个 sigmoid 神经元的隐藏层，我们可以得到一个更复杂的决策边界，可能会更紧密地适合我们的数据，但让我们把它留给编码部分。

在结束本节之前，我想回答一些剩余的问题:

#### 1-那么，特征工程和深度神经网络哪个更好？

嗯，答案取决于很多因素。一般来说，如果我们有大量的训练数据，我们可以使用深度神经网络来达到可接受的精度，但如果数据有限，我们可能需要执行一些特征工程，以从我们的神经网络中提取更多的性能。正如您在上面的特征工程示例中所看到的，要进行良好的特征交叉，您需要对他们正在处理的数据集有深入的了解。

特征工程和深度神经网络是一个强大的组合。

#### 2-如何统计一个神经网络的层数？

按照惯例，如果没有可调权重和偏差，我们不会计算层数。因此，虽然输入层是一个单独的“层”,但在指定神经网络的深度时，我们不将其计算在内。

所以，我们的最后一个例子是一个*“2 层神经网络”*(一个隐藏层+输出层)，在它之前的所有例子只是一个*“1 层神经网络”*(仅输出层)。

#### 3-为什么要使用激活功能？

***激活函数是非线性函数，给神经元增加非线性。特征十字是在隐藏层中堆叠激活功能的结果。*** 一堆激活函数的组合从而导致一个复杂的非线性决策边界。在这篇博客中，我们使用了 sigmoid/logistic 激活函数，但是还有许多其他类型的激活函数(ReLU 是隐藏层的流行选择)，每一种都有一定的好处。 ***在创建神经网络时，激活函数的选择也是一个超参数。***

***没有激活函数来增加非线性，无论我们叠加多少个线性函数，它们的结果仍然是线性的。*** 考虑以下情况:

<figure>

[![](img/317ca04ba5313ce7d9d516c1deb29ce3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8Mq4R7E6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/z83i6y1dfctujip3fdvg.png)

<figcaption>Fig 86\. Showing that stacking linear layers/functions results in a linear layer/function</figcaption>

</figure>

你可以使用任何非线性函数作为激活函数。有的研究者甚至用过 ***cos*** 、 ***sin*** 、 ***tan*** 函数。

#### 4-为什么随机初始化权重？

这个问题现在回答起来容易多了。请注意，如果我们将一个层中的所有权重设置为相同的值，那么通过每个节点的渐变将是相同的。简而言之，该层中的所有节点将学习关于数据的相同特征。将权重设置为 ***随机值有助于打破权重*** 的对称性，使得层中的每个节点都有机会学习训练数据的独特方面

在神经网络中有许多随机设置权重的方法。对于小型神经网络，将权重设置为较小的随机值是可以的。对于较大的网络，我们倾向于使用“Xavier”或“He”初始化方法(*将在编码部分*)。这两种方法仍然将权重设置为随机值，但控制它们的方差。*现在，可以说，当网络似乎没有收敛，并且当使用将权重设置为小随机值的“简单”方法时，成本变得静态或降低非常缓慢时，使用这些方法就足够了*。权重初始化是一个活跃的研究领域，将成为未来“除了数字什么都没有”博客的主题。

偏差也可以随机初始化。但在实践中，它似乎对神经网络的性能没有太大的影响。也许这是因为神经网络中偏向项的数量比权重少得多。

我们在这里创建的神经网络类型称为 ***“全连接前馈网络”*** 或简称为 ***“前馈网络”*** 。

第一部分到此结束。

* * *

## 第二部分:编码模块化神经网络

这部分的实现遵循 OOP 原则。

我们先来看看**线性层**类。构造函数将以下参数作为参数:传入数据的形状(`input_shape`)、层输出的神经元数量(`n_out`)以及需要执行哪种类型的随机权重初始化(`ini_type="plain"`，默认为“普通”，这只是小的随机高斯数)。

`initialize_parameters`是一个辅助函数，用于定义权重和偏差。稍后，我们将分别讨论它。

线性层实现以下功能:

*   `forward(A_prev)`:该功能允许线性层接收来自前一层的激活(输入数据可视为来自输入层的激活)并对其执行线性操作。
*   `backward(upstream_grad)`:该函数计算前一层的成本 w.r.t 权重、偏差和激活的导数(分别为`dW`、`db`、&、`dA_prev`)
*   `update_params(learning_rate=0.1)`:该函数使用`backward`函数中计算的导数对权重和偏差进行梯度下降更新。默认学习率(α)是 0.1*******************************