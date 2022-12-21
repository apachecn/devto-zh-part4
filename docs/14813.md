# 下载 Docker 图像，无需 Docker 拉动

> 原文：<https://dev.to/tomsfernandez/download-docker-images-without-docker-pull-17e6>

**Docker pull** 是使用 Docker 图像的基本命令。它大部分时间都工作正常，但有时会在下载过程中死机。很多人都提出了这个问题，包括终止一个进程或者多次重启 docker-machine。

对于像 *alpine* 分布图或 *nginx* 图像这样的小尺寸图像来说，所有这些问题都不是问题，但是对于大尺寸图像呢？重量约为 500 MB 或 2 GB 的图像？

这些方法没有用，因为它们需要反复下载大型图层。

一个更好的解决方法是手动下载图像，并将它们加载到 docker-machine。这是一个更好的解决方案，因为手动下载失败的几率比重启 docker-machine 并希望 docker pull 开始工作要低得多。

要手动下载 docker 映像，莫比项目中有一个名为“download-frozen-image-v2.sh”的脚本可以让我们这样做:

[https://github . com/Moby/Moby/blob/master/contrib/download-frozen-image-v2 . sh](https://github.com/moby/moby/blob/master/contrib/download-frozen-image-v2.sh)

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [莫比](https://github.com/moby) / [莫比](https://github.com/moby/moby)

### 莫比项目——集装箱生态系统组装基于集装箱的系统的合作项目

<article class="markdown-body entry-content container-lg" itemprop="text">

# 莫比项目

[![Moby Project logo](img/ea314beb7d1f0efe14e5921569e96f10.png "The Moby Project")](https://raw.githubusercontent.com/moby/moby/master/docs/static_files/moby-project-logo.png)

莫比是 Docker 创建的一个开源项目，旨在支持和加速软件容器化。

它提供了一个工具箱组件的“Lego set ”,将它们组装成定制的基于容器的系统的框架，并为所有容器爱好者和专业人员提供了一个实验和交流想法的地方。组件包括容器构建工具、容器注册中心、编排工具、运行时等等，这些可以与其他工具和项目一起用作构建块。

## 原则

莫比是一个开放的项目，由强有力的原则指导，旨在模块化，灵活，对用户体验没有太强的意见，它对社区开放，以帮助设置其方向。

*   模块化:该项目包括许多组件，这些组件具有良好定义的功能和协同工作的 API。
*   包括电池，但可更换:莫比包括足够的组件，以建立功能齐全的容器系统，但其模块化架构，确保大多数…

</article>

[View on GitHub](https://github.com/moby/moby)

这个脚本只需要安装三样东西:

*   卷曲
*   japan quarterly 日本季刊
*   去

要使用这个脚本，我们只需要指定要下载的图像和相对于当前的位置:

```
$ ./download-frozen-image-v2.sh "example" hello-world:latest 
```

在这个例子中，我们正在下载“example”目录中的图像“hello-world:latest”。

注意:一定要指定版本标签。如果未指定，脚本将返回未找到(404)

这会将几个文件下载到“示例”目录中:

*   manifest.json
*   储存库文件
*   带有图像细节的 json
*   图像中每个图层的文件夹

接下来唯一要做的事就是把它装入对接机。脚本输出必要的命令:

```
$ tar -cC 'example' . | docker load
af0b15c8625b: Loading layer [==================================================>]     977B/977B
Loaded image: hello-world:latest 
```

**恭喜**！您已经在没有 *docker pull* 命令的情况下提取了一个 docker 映像！