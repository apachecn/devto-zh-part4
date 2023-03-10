# 战斗还在继续:2019 年为 Raspberry Pi 构建 TensorFlow C 绑定

> 原文：<https://dev.to/martinezpeck/the-battle-continues-build-tensorflow-c-binding-for-raspberry-pi-in-2019-553j>

在最近的一篇帖子中，我展示了为 Raspberry Pi 和其他 SBC(单板计算机)构建 TensorFlow C 绑定仍然是多么具有挑战性，以及缺乏预构建的二进制文件。

正如您所看到的，我成功地使用了某种方法(用一个只有 RaspberryPi 的脚本进行交叉编译),但是我还不能在目标上编译(在这个例子中是 RaspberryPi 3 ),而且我仍然有很多问题没有解决。

在这篇文章中，我将展示一些更成功的尝试，并回答我之前的一些问题。

### 成功的尝试:在树莓派 3B+和 Raspbian Buster 上从头开始构建

我怀疑我一开始没有这样做的原因之一是交换空间和空闲硬盘空间的数量。

这是购买另一个 64GB 快速 micro SD、尝试 Raspbian Buster 并从全新安装开始的完美借口。所以，我就是这么做的。

如果你还记得上一篇文章，你需要做的第一件事就是安装 Bazel。在这种情况下，我做的第一件事就是给它大量的交换空间。我现在分配的是 8GB，而不是以前的 2GB。

接下来，记住你不需要任何版本的 Bazel。TensorFlow 需要的版本。我在之前的帖子中有一个公开的问题是“我是怎么知道的？”而[这里的](https://github.com/PINTO0309/Tensorflow-bin/issues/10)就是答案:你必须查看文件`tensorflow/tensorflow/tools/ci_build/install/install_bazel.sh`

例如，对于 1.13.1，您可以看到:

```
# Select bazel version.
BAZEL_VERSION="0.20.0"

set +e
local_bazel_ver=$(bazel version 2>&1 | grep -i label | awk '{print $3}')

if [[ "$local_bazel_ver" == "$BAZEL_VERSION" ]]; then exit 0
fi 
```

Enter fullscreen mode Exit fullscreen mode

好的，所以我按照上一篇文章的说明开始编译 Bazel，我发现了 Java VM 的问题。对不起，但是我没有写下确切的问题。但这是一个错误，显然与一些 Java 编译有关，而我在之前的尝试中没有遇到。

我的第一步是检查这个 Raspbian Buster 与我以前使用的 Raspbian Stretch 的 JVM 版本(`java --version`)。后者显示`java version “1.8.0_65″`，而前者显示`openjdk 11.0.3 2019-04-16`。好吧……所以 Stretch 用 Java 8，而 Buster 用 Java 11。

这是我想象 Bazel 可能只能用特定的 Java 版本来构建的时候。哪一个？不知道(如果知道，请告诉我)。所以我在新的 Raspbian Buster 上也安装了 Java 8:

```
sudo apt-get install openjdk-8-jdk 
```

Enter fullscreen mode Exit fullscreen mode

之后，您可以检查哪些是备选方案:

```
$ update-java-alternatives -l
java-1.11.0-openjdk-armhf      1111       /usr/lib/jvm/java-1.11.0-openjdk-armhf
java-1.8.0-openjdk-armhf       1081       /usr/lib/jvm/java-1.8.0-openjdk-armhf 
```

Enter fullscreen mode Exit fullscreen mode

显然，我不想为我的整个操作系统改变默认的 Java 版本，所以我只是做了如下的变通:

```
env BAZEL_JAVAC_OPTS="-J-Xms384m -J-Xmx1024m" \
JAVA_TOOL_OPTS="-Xmx1024m" \
JAVA_HOME="/usr/lib/jvm/java-1.8.0-openjdk-armhf" \
EXTRA_BAZEL_ARGS="--host_javabase=@local_jdk//:jdk" \
bash ./compile.sh 
```

Enter fullscreen mode Exit fullscreen mode

基本上，我只是告诉 Bazel 使用 Java 8。之后，它可以正确地结束。下一步是构建 TensorFlow。

我开始了我的构建过程，但是我遇到了另一个与 AWS 相关的编译问题，我可以通过告诉不要编译 AWS 来解决这个问题。

最后，13 个小时后(是的，这是预期时间)，我可以成功地建立它:

液体错误:内部

TensorFlow 的最终 Bazel 编译脚本是这样的:

```
env JAVA_HOME="/usr/lib/jvm/java-1.8.0-openjdk-armhf" \
bazel --host_jvm_args=-Xmx1024m --host_jvm_args=-Xms384m build \
--config=noaws \
--config opt --verbose_failures --local_resources 1024,1.0,1.0 \
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

有趣的地方:

*   我为 Java 8 添加了`JAVA_HOME`解决方法。
*   我把之前用的原来的`--jobs=3`去掉了，因为好像变化不大。
*   我为 AWS 添加了`--config=noaws`解决方法。
*   因为我知道编译会花很多时间，所以我想仔细检查 CPU 温度，所以每隔一段时间我就运行`/opt/vc/bin/vcgencmd measure_temp`并检查它的结果。

完成后，您可能希望将生成的文件复制到某个地方。在我的例子中，我做到了:

```
sudo cp bazel-bin/tensorflow/libtensorflow.so /usr/local/lib/
sudo cp bazel-bin/tensorflow/libtensorflow_framework.so /usr/local/lib/ 
```

Enter fullscreen mode Exit fullscreen mode

### 失败的尝试(但有收获！):在 Raspberry Pi 3B+和 Ubuntu Server 18.04 和 aarch64 (ARM 64 位)上从头构建

一旦我有了 ARM 32 位，我的下一步就是尝试 aarch64 (ARM 64 位)。在撰写本文时，还没有正式的 64 位 Raspbian 版本。这不是我第一次想用 aarch64 在 Pi3B+上运行一些东西，所以我已经有了一个运行 Ubuntu Server 18.04 的 micro SD。

我开始吃自己的狗粮了，第一个问题来了:Raspbian 和 Ubuntu 改变交换分区的方式不一样。所以我按照 Ubuntu 的指南分配了 8GB 的内存。

第二，看起来你需要安装`python`。不，这个 Ubuntu 没有安装任何 python。然后我明白了为什么有些博客文章以“首先，安装依赖项..”开始并在下面提供行:

```
sudo apt-get install gcc g++ swig build-essential openjdk-8-jdk python zip unzip 
```

Enter fullscreen mode Exit fullscreen mode

我让你决定是要 Python 2.x 还是 3x。正如我们之前发现的，JDK 版本也很重要。

无论如何，在那两个问题之后，我能够编译和运行 bazel 了。然而，当我试图为 TensorFlow 运行之前的 bazel 脚本时，我得到了一个编译错误，说`--copt=-mfpu=neon-vfpv4`不是一个可识别的选项:

[![](img/5712b02040eee9de3fea760836502570.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nf9igJ5O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://marianopeck.files.wordpress.com/2019/07/screen-shot-2019-07-23-at-9.47.58-am.png%3Fw%3D748)

感谢 [freedomtan](https://github.com/freedomtan) 他告诉我，我不需要所有那些额外的`--copt`和`--host_copt`(它们是为了复杂的拉斯皮恩环境)。因此，巴泽尔脚本应该更简单:

```
bazel --host_jvm_args=-Xmx1024m --host_jvm_args=-Xms384m build \
--config=noaws \
--config opt --verbose_failures --local_resources 1024,1.0,1.0 \
//tensorflow/tools/lib_package:libtensorflow 
```

Enter fullscreen mode Exit fullscreen mode

我以为这将会成功，但几个小时后它还是没有完成。不知何故挂了。我没有再抗争，因为这个 Ubuntu 的安装对我来说从来都不是真正稳定的。所以我会在不久的将来用 Debian Buster 或者 aarch64 的新版本再尝试一次。

### 成功尝试:说服别人来造！

在之前的帖子中，我评论过[这个 Github repo 为 TensorFlow 和 Raspberry Pi](https://github.com/PINTO0309/Tensorflow-bin) 提供二进制文件。缺点是他们提供的所有二进制文件都是 Python，而不是 C 共享库。

[在与作者](https://github.com/PINTO0309/Tensorflow-bin/issues/10)进行了愉快的讨论后，他现在为 ARM 和 ARM64 构建它！！似乎把它作为释放过程的一部分。但这还不是全部。似乎即使谷歌自己为 Raspberry Pi 提供了官方的 Python 轮子，仍有许多人使用他的构建。为什么？他解释道:

> 我的车轮档和官方车轮的区别是以下两点。
> 
> * tensor flow Lite 上默认禁用官方轮。但是，默认情况下，我的滚轮是启用的。
> 
> *调整为官方 Tensorflow Lite 性能的 2.5 倍。
> 
> 是 1。全世界的工程师都认为这是一个问题。

你可以在上面的链接中读到更多，看看如何获得这些二进制文件！顺便说一句，同一作者还[为 ARM](https://github.com/PINTO0309/Bazel_bin) 提供了 Bazel 的二进制文件！

### 未来的尝试(是的，旅程没有结束！)

*   使用[这些指令](https://github.com/PINTO0309/Tensorflow-bin)交叉编译我自己(为此目的的任何组合)。这种编译的主要问题是，由于某种原因[产生的共享库似乎与一个具体的 glibc 版本](https://github.com/PINTO0309/Tensorflow-bin/issues/10#issuecomment-514292503)相关联。因此，它的使用是有限的，因为它只能与匹配该 glibc 的操作系统一起工作(您可以在同一台主机上尝试多 glibc，但这似乎很痛苦)。如果你知道我们如何解决这个问题，请告诉我们。
*   使用这个[其他指令](https://github.com/lhelontra/tensorflow-on-arm)交叉编译我自己，这看起来也很有趣。不幸的是[作者对提供 C 绑定没有表现出太大的兴趣](https://github.com/lhelontra/tensorflow-on-arm/issues/69)。
*   在 Pi 上使用不同的操作系统(不是 Ubuntu)重试 aarch64 编译。