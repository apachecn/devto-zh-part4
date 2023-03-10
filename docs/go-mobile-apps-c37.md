# Go 用于构建移动 app

> 原文：<https://dev.to/uatthaphon/go-mobile-apps-c37>

Go 标志着一种新的、引人注目的语言，它除了在建立一个有速度的网站上独树一帜之外，还引起了许多人的兴趣。
t0㎡。

现在，Go 也在努力开发一个项目
来实现移动程序的开发，现在它被认为还处于开发阶段【23044】。com/golang/mobile〔T6〕字

但对于一个想在别人面前尝试新东西的人来说，朋友也不是问题，对吧？
如果你是另一个相信**的人，来吧。**

## 走手机

Go mobile 是 Go project 的一个子存储库，它同时支持 smart
【Android 和 iOS 电话】，并提供了一个用于 ga 的辅助工具。

在 Go 实现中，对于构建应用程序，可以分为两种类型:

1.  使用 Go 创建应用程序或让 Golang 成为本机应用程序
2.  将 Go 与本机应用程序或 SDK 应用程序和生成绑定结合在一起。

### 选择使用 Go 作为本机或使用 Go 作为良好的配套包。

在这方面，根据我们正在做或将要做的项目的性质，
我们来看看这两种项目的优点、缺点或局限性。

#### 使用 Go 创建应用程序，或让 Golang 作为本机应用程序。

就我们将使用 Go 作为本机的方式而言，这意味着我们选择使用 Go
语言作为构建应用程序的主要语言，此时(29)。

*   应用程序控制和配置
*   OpenGL ES 2 绑定
*   资产管理
*   事件管理
*   实验包包括 OpenAL 绑定、音频、字体、精灵和运动传感器

我们可以在[Golang 上浏览 go 国家应用程序代码的示例。org/x/mobile/example](https://godoc.org/golang.org/x/mobile/example)

#### [t1⾹与国家应用程序或 SDK 应用程序和生成绑定一起使用 Go](#%E0%B9%83%E0%B8%8A%E0%B9%89-go-%E0%B8%A3%E0%B9%88%E0%B8%A7%E0%B8%A1%E0%B8%81%E0%B8%B1%E0%B8%9A-native-application-%E0%B8%AB%E0%B8%A3%E0%B8%B7%E0%B8%AD-sdk-applications-and-generating-bindings)

Go 与国家应用相结合的优势

*   我们可以通过添加或添加到现有的 project 主体中来重用 Go package，而不必修改现有的 project 主体。
*   我们创建的可选的 Go package 主体可以应用于本机应用程序，无论是 Android 还是 iOS。

以下是包含的约束

*   现在只能支持几个类型，请参见[【go t1】类型的子集。](https://godoc.org/golang.org/x/mobile/cmd/gobind#hdr-Type_restrictions)
*   使用语言绑定会在处理问题上出现问题。
*   APIs 的运行是有限的，因此我们也需要研究我们将要实现的语言字符的限制。

## 安装和实验运行程序

我们已经知道，使用 Go 创建移动应用程序是在一起的。2Ӂt0⾹主要模型，在本文中，我们将执行安装和实验程序。

如果朋友想以其他方式预览安装，可以在 [Go 移动](https://github.com/golang/go/wiki/Mobile)上浏览。

> 对于 Go mobile 应用程序，我们必须执行 [Go 1 安装。5 或更高版本](https://golang.org/dl/)或至少需要 1 版本。7.4 万一你用的是 macOS sierra

首先，我们必须首先通过在您的终端或 iTerm
中执行以下命令来执行工具安装。

```
$ go get golang.org/x/mobile/cmd/gomobile
$ gomobile init # ใช้เวลาในการติดตั้งพอควร 
```

在我们完成了 gomobile 安装之后，
我们用命令在我们的机器上下载了一个示例。

```
$ go get -d golang.org/x/mobile/example/basic 
```

接下来，我们将执行我们刚刚下载的 project build，创建一个 apk
文件，以便在我们的智能手机上执行安装和测试。

```
$ gomobile build -target=android golang.org/x/mobile/example/basic 
```

> 万一出现了 error `gomobile: no Android NDK path is set. Please run gomobile init with the ndk-bundle installed through the Android SDK manager or with the -ndk flag set.`表示我们的 gomobile 找不到 ndk，所以让我们重新执行`gomobile init`命令，再加上一个外设。

```
$ gomobile init -ndk ~/Library/Android/sdk/ndk-bundle/ 
```

然后执行命令。

```
$ gomobile build -target=android golang.org/x/mobile/example/basic 
```

我们终于得到了 APK 文件。接下来，我们把已经在我们的手机上进行安装和测试的 APK
。