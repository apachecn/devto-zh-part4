# 接受挑战:2019 年为树莓 Pi 构建 TensorFlow C 绑定

> 原文：<https://dev.to/martinezpeck/challenge-accepted-build-tensorflow-c-binding-for-raspberry-pi-in-2019-4f89>

相信我。为 Raspberry Pi 设置环境和构建 TensorFlow C 绑定比训练一个让我靠机器人交易资产致富的神经网络更复杂。

### 动机

随着单板机(单板计算机)变得越来越强大和便宜，我们越来越有可能想在其上运行一些更繁重的计算。人们喜欢使用“边缘计算”、“嵌入式 HPC 或 ML”或类似的术语。

所有这些不同的 SBCs 替代品之间的共同点是使用 ARM 处理器加上某种类型的 GPU。

这种繁重计算的一个经典例子是 AI(人工智能)和 ML(机器学习)。在这个领域，使用和接受最多的库之一是 Google 的 [TensorFlow](https://www.tensorflow.org/) 。这样的库是用 Python 写的。不过，[也有预建 C、Java、Go 的官方二进制](https://www.tensorflow.org/install)。

C API 通常用于通过 FFI (外部函数接口)绑定到其他语言。在我看来，这是一个关键的二进制。

目前，我正在开发/测试一个通过 FFI 使用 C 库的 [VASmalltalk 绑定。我在 Linux x64 上测试，我在 Windows 上测试，然后我想在树莓派 3B+，Pine64，Nvidia Jetson Nano 等上尝试。为什么？因为我真的相信这个“嵌入式 ML”(或者不管你叫它什么)是有价值的。在一台 35 美元的机器上运行机器学习算法对我来说似乎很有趣。](https://github.com/vasmalltalk/tensorflow-vast)

液体错误:内部

那么…发生了什么？我干脆[去 TensorFlow 官网](https://www.tensorflow.org/install/lang_c#download)找共享库。你猜怎么着？根本没有。零。空。零。没有任何类型的 ARM 板的二进制文件。我太惊讶了，[我在 StackOverflow](https://stackoverflow.com/questions/56837317/how-can-i-get-a-tensorflow-c-binding-for-raspberry-pi) 问。

据我所知，市场上有很多主板，每种都有不同的硬件、软件、驱动程序、操作系统等。但我希望至少有一些非常常见的像树莓 Pi 和 Nvidia Jetson Nano。

总之…我的旅程就是这样开始的。我不确定我的作品对其他人是否有用，但至少对我未来的自己，我确信他们会有用。

接下来的部分是按照我寻找答案的顺序排列的。

> 声明:我不是 TensorFlow 专家。所以如果你有任何反馈，请分享！

### 失败尝试 1:安装 Python 版本，从那里解压共享库

随着一些最近的 TensorFlow 版本，Raspberry Pi / Raspbian 得到了官方支持(我认为> = 1.9)。然而，唯一可用的“二进制文件”是 Python wheels。我怀疑 Python 会在底层使用 C，所以我按照使用`pip`:
的官方指令[直接在我的 Pi 上安装 Python 版本](https://www.tensorflow.org/install/pip)

```
pip3 install --user --upgrade tensorflow # install in $HOME 
```

Enter fullscreen mode Exit fullscreen mode

然后我查看安装目录，发现了一些共享库！

```
cd /usr/local/lib/python3.5/dist-packages/tensorflow/python
ls -lah _pywrap_tensorflow_internal.so
-rwxr-xr-x 1 root staff 154M Jul  1 09:32 _pywrap_tensorflow_internal.so 
```

Enter fullscreen mode Exit fullscreen mode

但是你猜怎么着？`_pywrap_tensorflow_internal.so`不同于 C 绑定所需的共享库(`libtensorflow.so.1.14.0`)

我继续寻找，然后[我发现了一个 Docker](https://www.tensorflow.org/install/docker) 的装置。但是同样，只能为 Python 构建二进制文件，不能为 c。

在所有失败的尝试之后，[我在 Github](https://github.com/tensorflow/tensorflow/issues/30359) 上打开了一个案例，作为“功能请求”。

### 尝试 2 失败:寻找非官方的预编译二进制文件

显而易见的下一步是…“好吧，如果谷歌不做，那么其他人必须做”。我的意思是…不是只有 Smalltalk 想绑定 C 库，对吧？

长话短说，我发现没有。我找到了[这个](https://github.com/PINTO0309/Tensorflow-bin/issues/10)，但它只是 python 版本(但他说他可能会尝试提供共享库…所以继续关注吧！).然后我找到了[这个](https://dl.photoprism.org/tensorflow/)，至少对 Nvidia Jetson Nano 有效(但是我还没有带着 Nano)。我找到了[另一个](https://github.com/lhelontra/tensorflow-on-arm/issues/69)，但还是只有 Python。

所以…总之，我在任何地方都没有找到树莓 Pi 的共享库。如果你知道什么，请告诉我。最糟糕的是，大多数回答都是“你最好自己编译”。那听起来不太坏…我的意思是…当然，为什么不呢？直到我检查了 Linux x64 共享库的官方大小，`libtensorflow.so`是 216MB。WHATTTTTTTTTT？那一刻我想“好吧，这可不容易”。

### 放弃的尝试:在圆周率上从头开始构建

我下一个明显的步骤是尝试在 Pi 上从头开始构建。为此，我将我的工作建立在[这个非常有用的逐步指南](https://gist.github.com/EKami/9869ae6347f68c592c5b5cd181a3b205)的基础上。然而，自从指南编写以来，时间已经过去了，TensorFlow 变得“更容易”在 Pi 上构建，因此不再需要其中一些说明。此外，我还发现了我自己的问题，这些问题并没有在那里得到解决。

我建议您先阅读该指南，然后在这里继续。下面是我最后做的，和那个指南差不多。

在开始之前，我推荐一些重要的提示:

*   在你的 Pi 盘里有很多空闲的 GBs。
*   确保不要在 Pi 上运行任何繁重的任务(shutdown X，VNC，docker，任何可以使用 CPU 或内存的东西)。
*   从`ssh`开始运行构建。
*   使用`tmux`或类似的工具，因为这个过程需要几个小时(许多小时),所以您可能想要关闭您的开发机器并在第二天早上检查 Pi 状态。
*   如果你不想烧着它，就在你的 Pi 里使用散热片。

#### 建筑建造者:巴泽尔

首先，要构建 TensorFlow，你需要 bazel 工具。当然:`sudo apt-get install bazel`对吧？哈哈哈哈哈哈。LOL。我希望事情能这么简单。同样，看起来没有 bazel 包准备安装在 Pi 上。所以你必须先编译它。好吧…这东西越来越元了。我需要建造建造者…下一步是什么？来编译我将在其中构建构建器的 Linux 内核？…

现在…要编译 bazel 或 TensorFlow，在这两种情况下，Pi 的 1GB RAM 都不够。所以必须增加交换空间。在提到的指南中，它安装了一个外部 u 盘/硬盘等。在我的例子中，我只是将 SD 卡的交换分区增加到 2GB。但是人们推荐更多的…比如 8GB(但我没有那么多空闲):

```
sudo vim /etc/dphys-swapfile # change CONF_SWAPFILE to 2000
sudo /etc/init.d/dphys-swapfile stop
sudo /etc/init.d/dphys-swapfile start
free -m # confirm we have now 2000mb 
```

Enter fullscreen mode Exit fullscreen mode

> 重要提示:无论 bazel 和 TensorFlow 编译成功与否，完成后将交换空间大小(`CONF_SWAPFILE`)恢复到原始大小是非常重要的。否则，你会毁了 SD 的寿命。

bazel 的编译可能需要几个小时。当我完成并开始编译 TensorFlow 时，我收到了一条很棒的消息:

> 请将您的 bazel 安装降级到版本 0.21.0 或更低版本以构建 TensorFlow！

你在开玩笑吗？？？？？？我花了几个小时编译错误的巴泽尔版本？FUC……有没有办法提前知道每个 TensorFlow 版本需要哪个 bazel 版本？我不知道。如果你知道，请告诉我。反正我是重新开始了我想要的 TensorFlow 的版本(1.13.1)它需要的版本:

```
mkdir bazel
cd bazel 
wget https://github.com/bazelbuild/bazel/releases/download/0.21.0/bazel-0.21.0-dist.zip
unzip bazel-0.21.0-dist.zip
env BAZEL_JAVAC_OPTS="-J-Xms384m -J-Xmx1024m" \
JAVA_TOOL_OPTS="-Xmx1024m" \
EXTRA_BAZEL_ARGS="--host_javabase=@local_jdk//:jdk" \
bash ./compile.sh
sudo cp output/bazel /usr/local/bin/bazel
cd ..
rm -rf bazel* 
```

Enter fullscreen mode Exit fullscreen mode

内存的 Java 选项(`1024`是因为 Pi 3B+有 1GB RAM)是必要的，因为否则编译就会失败(感谢 [freedomtan](https://github.com/freedomtan) 的帮助)。不，它不会因为一个漂亮的“内存不足”而失败，而是某种随机错误。[我在 Github 上报道了这件事。](https://github.com/bazelbuild/bazel/issues/8882)

其他必要部分`--host_javabase=@local_jdk//:jdk`。我甚至不记得为什么…没有它就不会工作。

如果你成功地做到了这一点，那么把这个`bazel`二进制文件保存到任何地方！不要松了哈哈哈哈。同样，如果你知道哪里可以找到 bazel 预编译的 Pi 二进制文件，请告诉我。

#### Building TensorFlow

第一步很简单:

```
git clone --recurse-submodules https://github.com/tensorflow/tensorflow.git
cd tensorflow
git checkout v1.13.1
./configure 
```

Enter fullscreen mode Exit fullscreen mode

`./configure`会问你几个问题，关于你想在即将进行的 TensorFlow 编译中添加什么支持。答案将取决于您的目标硬件。对于 Raspberry Pi，我认为可以简单地回答所有问题都是错误的:

[![](img/84fc1072f08ff3f6e7561aa996799d55.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FQVUgGIp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://marianopeck.files.wordpress.com/2019/07/screen-shot-2019-07-13-at-2.30.07-pm.png%3Fw%3D748)

看着这些问题，你可能会知道你最终会为 Nvidia Jetson、Parallella Board 等回答什么。是的，我想看看它是否能在 Parallella 板上工作:

液体错误:内部

最后，是运行编译的时候了。不，不要拿啤酒，你会喝醉的。不，不要喝咖啡……你会喝下太多的咖啡因，以至于一整个星期都无法入睡。

```
bazel --host_jvm_args=-Xmx1024m --host_jvm_args=-Xms384m build \
--config opt --verbose_failures --jobs=3 --local_resources 1024,1.0,1.0 \
--copt=-mfpu=neon-vfpv4 \
--copt=-ftree-vectorize \
--copt=-funsafe-math-optimizations \
--copt=-ftree-loop-vectorize \
--copt=-fomit-frame-pointer \
--copt=-DRASPBERRY_PI \
--host_copt=-mfpu=neon-vfpv4 \
--host_copt=-ftree-vectorize \
--host_copt=-funsafe-math-optimizations \
--host_copt=-ftree-loop-vectorize \
--host_copt=-fomit-frame-pointer \
--host_copt=-DRASPBERRY_PI \
//tensorflow/tools/lib_package:libtensorflow 
```

Enter fullscreen mode Exit fullscreen mode

关于这一点有一些有趣的地方:

*   大部分的`--copt`和`--host_copt` [都没有被我](https://github.com/tensorflow/tensorflow/issues/30359)识别出来。再次感谢 [freedomtan](https://github.com/freedomtan) 。
*   我已经解释了为什么 Java 内存参数？
*   如果我们的构建没有得到一些出错的描述，这是很有用的。
*   `--local_resources`帮助指定使用“多少硬件资源”。
*   对我来说，由于资源不足，它仍然没有建成。所以我最后添加了`--jobs=3`,这最小化了资源的使用(但显然会花费更长的时间)。[我从一个 StackOverflow](https://stackoverflow.com/questions/34382360/decrease-bazel-memory-usage) 那里得到这个。
*   有趣的是，构建 Python Wheel 或共享库几乎是相同的过程。唯一的变化就是不再是`//tensorflow/tools/lib_package:libtensorflow` (for。所以)你用`//tensorflow/tools/pip_package:build_pip_package`得到一个轮子。这就是为什么我友好地要求那些已经提供轮子的人也提供共享库。
*   这个过程需要很多很多个小时(在我的例子中超过 20 个小时)。所以，睡觉，第二天早上检查。

这个**应该**管用。然而，由于它花费了太多的时间，我继续寻找其他替代方案，我从来没有真正让这个过程完成。所以我不能确定它是否有效。现在我的 SD 没有空闲空间，而我已经有了一个工作。所以(下一节)。如果你尝试了，并且成功了，请告诉我！否则，我想我会在不久的将来再试一次。

### 最终尝试:交叉编译

当我等待 Pi 上的编译完成，看着它的绿色 led 灯不停地亮了几个小时时，我继续寻找更多的替代方案。一个偶然的机会，我到达了一个官方链接，展示了如何为圆周率交叉编译 TensorFlow。(这个我早该看出来了！哈哈哈哈哈)

为了理解准备好所有的环境设置有多困难，假设交叉编译过程是使用 Docker 并从他们提供的现有映像开始…

这个过程看起来非常简单:安装 docker，然后运行一个 shell 行:

```
tensorflow/tools/ci_build/ci_build.sh PI \
    tensorflow/tools/ci_build/pi/build_raspberry_pi.sh 
```

Enter fullscreen mode Exit fullscreen mode

酷毙了。听起来很神奇。好得难以置信。然后，我准备好利用我的 MBP 的所有 8 个 CPU 内核和 16 GB RAM。不幸的是，这个过程对我来说并不顺利。每次运行都会在不同的地方失败，原因也不清楚。同样，[我在 Github](https://github.com/tensorflow/tensorflow/issues/30764) 上开了一个案例，但是至今没有回应。

我当时正要放弃在 ARM / SBC 上对 TensorFlow 的所有尝试。但是我还有最后一个想法:用 Docker 再次尝试这种交叉编译，但是现在是在我用 Linux Mint 18.3 安装的 Linux 虚拟机上。当然，这个虚拟机永远不会像直接在我的主机(OSX)上运行那样快，但它应该比在 Pi 上运行要快得多。

不管这算不算奇迹，但几个小时后，这确实奏效了。我成功地获得了`.so`，将它移动到 Pi 中，然后运行我的测试。一切正常:

液体错误:内部

### 结论

我希望 Google 能正式发布 C 二进制文件，至少是最常见的 SBC，比如 Raspberry Pi 或 Jetson Nano。如果不是这样，我希望那些已经在为 Raspberry 编译 Wheels 的人也能编译共享库。

最后，尽管如此，我认为从头开始学习构建的底层细节是值得的。为什么？因为我想尝试的主板有很多:Rpi3，但使用 ARM 64 OS (Armbian，Ubuntu Server 18.04 等)，Rpi4，Pine64，Jetson Nano 等。我们甚至可以在 Nvidia Jetson TX2 上测试！！！对于所有这些情况，我不能使用现成的交叉编译替代方法，因为它只适用于 Pi。

液体错误:内部

我希望除了未来的我，我还能帮助其他人。如果您有任何反馈，请分享！

## [更新:关于这个话题请看下篇帖子！](https://dev.to/martinezpeck/the-battle-continues-build-tensorflow-c-binding-for-raspberry-pi-in-2019-553j)