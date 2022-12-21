# 密集网络 PyTorch 图像识别

> 原文：<https://dev.to/nestedsoftware/pytorch-image-recognition-dense-network-3nbd>

在上一篇文章中，我们验证了一个只有 *2* 个神经元的小型网络的人工反向传播计算与 PyTorch 的结果相匹配。我们将在本文中以类似的精神继续:这一次我们将实现一个完全连接的或密集的网络，用于从 MNIST 数据库中识别手写数字( *0* 到 *9* ，并将其与迈克尔·尼尔森的书[神经网络和深度学习](http://neuralnetworksanddeeplearning.com/)的[第 1 章](http://neuralnetworksanddeeplearning.com/chap1.html#exercise_358114)中描述的结果进行比较。

这个项目的[代码](https://github.com/nestedsoftware/pytorch)可以在 github 上获得。

## 网络结构

迈克尔·尼尔森在第一章中描述的网络将 28 x 28 灰度像素 MNIST 图像作为输入，并通过一个完全连接的隐藏层运行，该隐藏层由 100 个乙状激活神经元组成。这个隐藏层然后馈入一个完全连接的输出层，这个输出层由 *10* 乙状窦激活的神经元组成。输出层中的每个神经元代表一个数字，因此激活度最高的输出神经元代表网络的预测。更多细节，你也可以看看我的文章，[神经网络初级读本](https://dev.to/nestedsoftware/neural-networks-primer-374i)

描述本文将使用的简单网络的代码如下所示( [pytorch_mnist.py](https://github.com/nestedsoftware/pytorch/blob/master/pytorch_mnist.py) ):

```
INPUT_SIZE = 28 * 28
OUTPUT_SIZE = 10
NUM_EPOCHS = 30
LEARNING_RATE = 3.0

class Net(nn.Module):
    def __init__(self):
        super(Net, self).__init__()
        self.hidden_layer = nn.Linear(INPUT_SIZE, 100)
        self.output_layer = nn.Linear(100, OUTPUT_SIZE)

    def forward(self, x):
        x = torch.sigmoid(self.hidden_layer(x))
        x = torch.sigmoid(self.output_layer(x))
        return x 
```

## 运行网络

下面的高级代码加载数据，使用训练数据集训练网络，然后使用测试数据集测试网络( [pytorch_mnist.py](https://github.com/nestedsoftware/pytorch/blob/master/pytorch_mnist.py) ):

```
def run_network(net):
    mse_loss_function = nn.MSELoss()
    sgd = torch.optim.SGD(net.parameters(), lr=LEARNING_RATE)

    train_loader = get_train_loader()
    train_network(train_loader, net, NUM_EPOCHS, sgd,
                  create_input_reshaper(),
                  create_loss_function(mse_loss_function))

    print("")

    test_loader = get_test_loader()
    test_network(test_loader, net, create_input_reshaper()) 
```

我们使用随机梯度下降来更新权重和偏差，并使用简单的均方误差函数来计算成本或损失。术语*二次成本函数*和*均方误差损失函数*指的是同一事物。

## 数据加载

PyTorch 为 MNIST 数据集(以及其他数据集)提供了方便的内置加载器，因此我们不必从头开始编写代码来下载和准备数据。让我们来看看这是如何工作的( [common.py](https://github.com/nestedsoftware/pytorch/blob/master/common.py) ):

```
import torch
import torchvision.transforms as transforms
import torchvision.datasets as datasets

BATCH_SIZE = 10

# transforms each PIL.Image to a tensor that can be used as input in pytorch transformations = transforms.Compose([transforms.ToTensor()])

def get_dataset(root="./data", train=True, transform=transformations,
                download=True):
    return datasets.MNIST(root=root, train=train, transform=transform,
                          download=download)

def get_loader(dataset, batch_size=BATCH_SIZE, shuffle=True):
    return torch.utils.data.DataLoader(dataset=dataset, batch_size=batch_size,
                                       shuffle=shuffle)

def get_train_loader():
    train_dataset = get_dataset()
    train_loader = get_loader(train_dataset)
    return train_loader

def get_test_loader():
    test_dataset = get_dataset(train=False)
    test_loader = get_loader(test_dataset, shuffle=False)
    return test_loader 
```

`torchvision.datasets.MNIST`对象表示来自 MNIST 数据库的数据。默认情况下，每张图片都是 PIL T2 的格式。PyTorch 允许我们在生成数据集时提供转换。在这里，我们只是将数据集中的图像从 PIL 格式转换成 PyTorch 张量，但是还有更强大的工具[来处理输入的数据。我们使用以下标志:](https://pytorch.org/tutorials/beginner/data_loading_tutorial.html#transforms)

*   `download`标志允许我们在必要时从互联网下载数据，然后将其存储在本地(在本例中为`"./data"`)。一旦数据集被存储，下次就在本地加载。

*   `train`标志确定是加载训练数据集( *60，000 张*图像)还是测试数据集( *10，000 张*图像)。

由`torch.utils.data.DataLoader`创建的对象允许我们在数据集加载后对其进行操作。我们使用的[选项](https://pytorch.org/docs/stable/data.html)是`batch_size`和`shuffle`:

*   `batch_size`表示在更新权重和偏差之前通过给定网络的图像数量。为了提高性能，神经网络通常采用一些随机梯度下降的变化进行反向传播:我们一次通过网络运行多个图像。在更新权重和偏差之前，将得到的梯度一起平均。每次我们从加载器的迭代器中获取下一项时，该项将包含许多对应于为`batch_size`设置的值的图像。在上面的代码中，我们使用每批 *10 个*图像来匹配迈克尔·尼尔森书中的配置。

*   `shuffle`告诉我们在迭代数据之前是否要随机化数据集中的项目顺序。对于神经网络，我们通常会在整个训练数据集上多次训练网络。术语*纪元*用于描述我们每次遍历训练数据集中的所有数据。为了提高网络归纳以前没有见过的新数据的能力，使用`shuffle`选项对每个这样的训练时期的数据进行重新排序。

## MNIST 数据的结构

让我们使用 Python REPL 来探索数据的结构:

```
>>> import common as c
>>> train_dataset = c.get_dataset()
>>> len(dataset)
60000
>>> test_dataset = c.get_dataset(train=False)
>>> len(train_dataset)
10000 
```

上面，我们可以看到我们的训练数据集包含 *60，000* 张图像，我们的测试数据集包含 *10，000* 张图像。数据加载器准备的数据是什么样子的？

```
>>> import common as c
>>> data_loader = c.get_test_loader()
>>> images, expected_results = next(iter(data_loader))
>>> len(images)
10
>>> len(expected_results)
10
>>> expected_results
tensor([7, 2, 1, 0, 4, 1, 4, 9, 5, 9]) 
```

对于每个批处理，我们的加载器返回一个包含两项的元组:元组中的第一项是该批处理中图像的数组。第二项是图像要表示的实际值的数组。在这里，因为我们已经将`batch_size`设置为 10，所以我们从测试数据加载器获得了一个由 *10* 图像和 *10* 预期结果组成的元组。上面的代码显示了第一批加载的测试图像的期望值。现在让我们仔细看看图像本身:

```
>>> images.size()
torch.Size([10, 1, 28, 28]) 
```

我们可以这样想:有 *10 个*图像，每个图像是一个三维数组，大小为`(1, 28, 28)`。 *1* 表示每个图像都有一个通道。MNIST 数据是灰度的，每个像素是一个在 *0.0* (黑色)和 *1.0* (白色)之间的值。对于彩色图像，将有 3 个*通道(通常是红色、绿色和蓝色)，在每个通道中，将有一个值的二维数组来表示图像中该颜色的强度。*

来自加载器的数据已经被正确设置，可以用作卷积神经网络的输入，我们将在下一篇文章中讨论。然而，在这种情况下，我们有一个完全连接的隐藏层。因此，正如在[神经网络初级读本](https://dev.to/nestedsoftware/neural-networks-primer-374i)中所讨论的，我们需要将每个图像展平成一维数组。在 PyTorch 中有一个简单的方法:

```
>>> images_reformatted = images.reshape(-1, 28*28)
>>> images_reformatted.size()
torch.Size([10, 784]) 
```

当我们提供 *-1* 作为`images.reshape`的参数时，它被当作一个占位符。PyTorch 知道数组中值的总数是*10 * 1 * 28 * 28 = 7840*。由于我们指定希望数组的第二维大小为 *28 * 28* 或 *784* ，PyTorch 可以计算出 *-1* 必须对应于 *10* 。这让我们将批处理中的每个 *1 x 28 x 28* 图像转换成一个 *784* 像素阵列(在此过程中移除不必要的颜色通道)。只要我们知道我们想要的输入大小，不管每批中的图像数量是多少，这都是可行的。

## 训练前后的网络输出

我们现在可以使用这些重新格式化的图像作为我们完全连接的网络的输入。让我们在 Python REPL 中通过我们的网络运行第一批图像:

```
>>> import torch
>>> import common as c
>>> import pytorch_mnist as mnist
>>>
>>> data_loader = c.get_test_loader()
>>> images, expected_results = next(iter(data_loader)) # batch from test data >>> expected_results[0]
tensor(7) # the expected result is a 7 for the first test image >>>
>>> images_reformatted = images.reshape(-1, 28*28)
>>> net = mnist.Net()
>>> outputs = net(images_reformatted) # before training, outputs for test batch >>> outputs[0] # outputs for first test image are scattered randomly tensor([0.4313, 0.4844, 0.5163, 0.5135, 0.5347, 0.5641, 0.4823, 0.4459, 0.5161,
        0.4919], grad_fn=<SelectBackward>) 
```

我们从测试数据集中获得第一批图像，重新格式化它们，然后一次性将它们作为输入发送到我们的网络。网络忠实地返回 *10* 输出。每个输出是一个由 *10* 个浮点值组成的数组。上面，我们显示了第一幅图像的网络输出。由于网络还没有训练好，所以输出值都是随机的。网络经过训练后，应该有一个值非常接近 *1.0* ，对应预测的位数，其余的应该非常接近 *0.0* 。

让我们继续我们的 REPL 会议。下面，我们将训练网络，然后再次检查第一幅图像的输出:

```
>>> mnist.run_network(net) # now let's train the network Epoch [1/30], Step [100/6000], Loss: 0.0797
# ...omitting intermediate steps Epoch [30/30], Step [6000/6000], Loss: 0.0001

Test data results: 0.9785
>>> outputs = net(images_reformatted) # after training, outputs for test batch >>> outputs[0]
tensor([1.3546e-04, 4.1909e-06, 5.8085e-06, 9.5294e-05, 4.5417e-07, 5.6720e-04,
        1.3523e-09, 9.9983e-01, 1.4206e-05, 1.7880e-05],
       grad_fn=<SelectBackward>)
>>> output_value, output_index = torch.max(outputs[0], 0)
>>> output_value
tensor(0.9998, grad_fn=<MaxBackward0>) # the highest output value is almost 1 >>> output_index # the predicted digit, i.e. the index of the highest value is 7 tensor(7)
>>> c.transforms.ToPILImage()(images[0]).show() 
```

最初，第一幅图像的所有输出值都非常随机，对应于数字 *5* 的值最高( *0.5641* )。在训练网络之后，所有的输出值现在都接近于零，除了对应于 7 的值，即 *0.9998* ，或者几乎是 *1* 。测试数据中对应的目标值其实是 *7* ，所以网络现在猜测正确！下面是上面最后一行代码显示的图像的放大版本:

[![mnist test image](img/e3ecdb1c901e7f8ca6000aa919b05ca0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oe3vXoKD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tu6jr7pwervlz93zm32l.png)

我们还可以看到，经过 *30* 次训练后，我们的网络对测试数据的准确率为 *97.85%* 。这比 Michael Nielsen 发布的 *96.59%* 略高，这是对 *126* 张图像(测试数据集中的 *10，000* 张图像)的改进。总的来说，这些结果似乎与书中的发现相当一致。

## 计算损失函数

下面是训练网络的代码( [common.py](https://github.com/nestedsoftware/pytorch/blob/master/common.py) ):

```
def train_network(data_loader, model, num_epochs, optimizer, reshape_input,
                  calc_loss):
    num_batches = len(data_loader)
    for epoch in range(num_epochs):
        for batch in enumerate(data_loader):
            i, (images, expected_outputs) = batch

            images = reshape_input(images)
            outputs = model(images)
            loss = calc_loss(outputs, expected_outputs)

            optimizer.zero_grad()
            loss.backward()
            optimizer.step()

            if (i+1) % 100 == 0 or (i+1) == num_batches:
                p = [epoch+1, num_epochs, i+1, num_batches, loss.item()]
                print('Epoch [{}/{}], Step [{}/{}], Loss: {:.4f}'.format(*p)) 
```

注意，我们将`calc_loss`作为对`train_network`的回调传入。该函数计算预期值相对于网络实际输出的成本或损失。对于这个网络，我们使用均方误差损失函数。对于一个给定的图像，我们的网络输出一个由 *10* 个值组成的数组，其中每个值代表该值的索引对应于输入所代表的数字的置信度。我们从数据加载器得到的预期输出只是实际数字，从 *0* 到 *9* 。因此，对于反向传播，我们需要将预期的输出转换为与网络输出相匹配的值的数组。进行这种转换的代码如下所示( [pytorch_mnist.py](https://github.com/nestedsoftware/pytorch/blob/master/pytorch_mnist.py) ):

```
def expand_expected_output(tensor_of_expected_outputs, output_size):
    return torch.tensor([expand_single_output(expected_output.item(),
                                              output_size)
                         for expected_output in tensor_of_expected_outputs])

# Expand expected output for comparison with the outputs from the network,
# e.g. convert 3 to [0., 0., 0., 1., 0., 0., 0., 0., 0., 0.] def expand_single_output(expected_output, output_size):
    x = [0.0 for _ in range(output_size)]
    x[expected_output] = 1.0
    return x 
```

就像我们之前看到的，假设给定批次的预期输出存储为`tensor([7, 2, 1, 0, 4, 1, 4, 9, 5, 9])`。`expand_expected_output`将把这个张量作为`tensor_of_expected_outputs`的输入， *10* 作为`output_size`的输入。对于每个期望值，它将生成一个数组，其中 9 个值被设置为 *0.0* ，与正确输出相对应的索引处的单个值被设置为 *1.0* :

```
>>> import torch
>>> import pytorch_mnist as mnist
>>> expected_results = torch.tensor([7, 2, 1, 0, 4, 1, 4, 9, 5, 9])
>>> mnist.transform_expected_output(expected_results, 10)
tensor([[0., 0., 0., 0., 0., 0., 0., 1., 0., 0.],
        [0., 0., 1., 0., 0., 0., 0., 0., 0., 0.],
        [0., 1., 0., 0., 0., 0., 0., 0., 0., 0.],
        [1., 0., 0., 0., 0., 0., 0., 0., 0., 0.],
        [0., 0., 0., 0., 1., 0., 0., 0., 0., 0.],
        [0., 1., 0., 0., 0., 0., 0., 0., 0., 0.],
        [0., 0., 0., 0., 1., 0., 0., 0., 0., 0.],
        [0., 0., 0., 0., 0., 0., 0., 0., 0., 1.],
        [0., 0., 0., 0., 0., 1., 0., 0., 0., 0.],
        [0., 0., 0., 0., 0., 0., 0., 0., 0., 1.]]) 
```

在权重和偏差更新后，对于同一幅图像，这将向下推动分配给 *0.0* 的输出，向上推动分配给 *1.0* 的输出。

## 测试网络

训练完网络后，下面的`test_network`函数检查它有多少预测是正确的( [common.py](https://github.com/nestedsoftware/pytorch/blob/master/common.py) ):

```
def test_network(data_loader, model, reshape):
    with torch.no_grad():
        correct = 0
        total = 0
        for batch in data_loader:
            images, expected_outputs = batch

            images = reshape(images)
            outputs = model(images)

            # get the predicted value from each output in the batch
            predicted_outputs = torch.argmax(outputs, dim=1)

            total += expected_outputs.size(0)
            correct += (predicted_outputs == expected_outputs).sum()

        print(f"Test data results: {float(correct)/total}") 
```

关于此函数中代码的一些注释:

*   `torch.no_grad()`当我们只想从网络获得输出，并且不担心更新梯度时，减少内存使用

*   `torch.argmax(outputs, dim=1)`给出每行输出中最大值的索引。设置`dim=0`将给出每列中最大值的索引。

*   如果`expected_outputs`是一个含有 *10 个*项的一维张量，那么它的大小将是`torch.Size([10])`。`expected_outputs.size(0)`从`Size`对象中检索第一个(在本例中是唯一的)项目，即值 *10* 。这为我们提供了该批次的预期输出总数，然后我们可以从中计算出匹配的实际输出的比例。

*   `predicted_outputs == expected_outputs`产生一个张量，当`predicted_outputs`和`expected_outputs`张量中的值匹配时，其*为 1* ，当它们不匹配时，其*为 0* 。因此，`sum()`为我们提供了一批图像的正确预测数。

## 演示

这就完成了概述。尽管我们实现的网络非常简单，但我们已经了解了很多关于 PyTorch 的重要基础知识。从 github 下载代码后(见下文)，您可以运行本文附带的演示程序，如下所示:

```
C:\Dev\python\pytorch>python pytorch_mnist.py
Epoch [1/30], Step [100/6000], Loss: 0.0885
...
Epoch [30/30], Step [6000/6000], Loss: 0.0025

Test data results: 0.9796 
```

## 代码

本文的完整代码可以在 github 上找到:

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [嵌套软件](https://github.com/nestedsoftware) / [ pytorch](https://github.com/nestedsoftware/pytorch)

### PyTorch 基本用法演示。包括使用密集以及卷积网络的 MNIST 识别。

<article class="markdown-body entry-content container-lg" itemprop="text">

这个项目包含了演示 PyTorch 基本用法的脚本。代码需要 python 3、numpy 和 pytorch。

## 手动与 PyTorch 反向投影计算

要将手动反向投影计算与等效 PyTorch 版本进行比较，请运行:

```
python backprop_manual_calculation.py
w_l1 = 1.58
b_l1 = -0.14
w_l2 = 2.45
b_l2 = -0.11
a_l2 = 0.8506
updated_w_l1 = 1.5814
updated_b_l1 = -0.1383
updated_w_l2 = 2.4529
updated_b_l2 = -0.1062
updated_a_l2 = 0.8515 
```

和

```
python backprop_pytorch.py
network topology: Net(
  (hidden_layer): Linear(in_features=1, out_features=1, bias=True)
  (output_layer): Linear(in_features=1, out_features=1, bias=True)
)
w_l1 = 1.58
b_l1 = -0.14
w_l2 = 2.45
b_l2 = -0.11
a_l2 = 0.8506
updated_w_l1 = 1.5814
updated_b_l1 = -0.1383
updated_w_l2 = 2.4529
updated_b_l2 = -0.1062
updated_a_l2 = 0.8515 
```

博文: [PyTorch Hello World](https://dev.to/nestedsoftware/pytorch-hello-world-37mo)

## MNIST 认可

接下来的例子首先使用密集的网络识别 MNIST 数字，然后是几个卷积网络设计(例子改编自 Michael Nielsen 的书《神经网络和深度学习》)。

我添加了…

</article>

[View on GitHub](https://github.com/nestedsoftware/pytorch)

## 参考文献:

*   《神经网络和深度学习》第 1 章，作者迈克尔·尼尔森
*   [指针](https://github.com/nestedsoftware/pytorch)
*   [PIL - Python 图像库](https://en.wikipedia.org/wiki/Python_Imaging_Library)
*   [数据加载器选项](https://pytorch.org/docs/stable/data.html)
*   [PyTorch 数据加载和处理教程-转换](https://pytorch.org/tutorials/beginner/data_loading_tutorial.html#transforms)

## 相关

*   [神经网络初级读本](https://dev.to/nestedsoftware/neural-networks-primer-374i)