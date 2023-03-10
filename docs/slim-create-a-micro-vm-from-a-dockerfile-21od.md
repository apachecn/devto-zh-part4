# slim:从 Dockerfile 文件创建一个微型虚拟机

> 原文：<https://dev.to/chrisparnin/slim-create-a-micro-vm-from-a-dockerfile-21od>

将从 Dockerfile 文件构建一个微型虚拟机。Slim 的工作方式是从 docker 文件中构建并提取一个 rootfs，然后将该文件系统与一个运行在 RAM 中的小型内核合并。

这使得真正的虚拟机可以立即启动，同时使用非常有限的资源。如果处理得当，slim 可以让您设计和构建不可变的 unikernels 来运行服务，或者构建微小的嵌入式开发环境。

### 构建微型虚拟机

从 Dockerfile 文件创建一个微型虚拟机。对包含 [Dockerfile](https://github.com/ottomatica/slim/tree/mastimg/alpine3.8-simple) 的目录使用`build`命令。

```
$ slim build images/alpine3.8-simple 
```

生成的图像只有 23MB！它可用于虚拟机箱、kvm 或 hyper-kit。

### 运行微型虚拟机

将给定微虚拟机映像的新实例配置为虚拟机。

```
$ slim run micro1 alpine3.8-simple 
```

### 演示

这个简短的演示演示了如何构建一个映像，用 hyper kit 在 3 秒钟内启动，然后从 ssh 访问虚拟机。

[![slim](img/030ac6f0154d54797e7de1afdebe4c00.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--a9SRwtOw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2exohq10w0akfwzod9xc.gif)

### 发现更多

请参见 [GitHub repo](https://github.com/ottomatica/slim/) 了解更多信息！