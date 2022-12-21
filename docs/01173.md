# SwiftUI 中的远程图像

> 原文：<https://dev.to/gualtierofr/remote-images-in-swiftui-49jp>

原帖[http://www.gfrigerio.com/remote-images-in-swiftui/](http://www.gfrigerio.com/remote-images-in-swiftui/)

几乎每个 iOS 应用程序都需要显示图像，你可以将它们放在你的应用程序包中，或者从外部 URL 加载它们，这就是本文的内容。
像往常一样，你可以在 GitHub 的[链接](https://github.com/gualtierofrigerio/StargazersSwiftUI)找到源代码

## 构建 UI

在这个例子中，我展示了一个来自 GitHub 库的观星者列表，我想展示他们的头像和名字。观点真的很简单

```
struct StargazerView: View {
    var stargazer:User

    var body: some View {
        HStack {
            ImageView(withURL: stargazer.avatarUrl)
            Text(stargazer.login)
        }
    }
}

struct StargazersView: View {
    @ObservedObject var viewModel:StargazersViewModel

    var body: some View {
        VStack {
            List(viewModel.stargazers) { stargazer in
                StargazerView(stargazer: stargazer)
            }
            Button(action: {
                self.viewModel.getNextStargazers { success in
                    print("next data received")
                }
            })
            {
                Text("next")
            }
        }
    }
}

struct ImageView: View {
    @ObservedObject var imageLoader:ImageLoader
    @State var image:UIImage = UIImage()

    init(withURL url:String) {
        imageLoader = ImageLoader(urlString:url)
    }

    var body: some View {
        VStack {
            Image(uiImage: image)
                .resizable()
                .aspectRatio(contentMode: .fit)
                .frame(width:100, height:100)
        }.onReceive(imageLoader.didChange) { data in
            self.image = UIImage(data: data) ?? UIImage()
        }
    }
} 
```

让我们来看看 ImageView。我需要一个 UIImage，我将转换为 SwiftUI 图像，正如你可以看到的调用图像(uiImage:image)，我用一个空图像初始化它，但你可以从你的资产目录设置一些东西，例如一个空的头像图像。我们将看一下 ImageLoader，但首先让我们看看我们的视图如何处理被获取的图像，以及在视图被加载后变得可用。在 init 中，我们创建了一个 ImageLoader 实例，它带有我们想要获取的 url，这是一个 ObservedObject。我们需要使用一个符合 ObervableObject 的类型，以便将其用作@ObservedObject，当数据准备好时，该对象需要通知视图。我将向您展示两种方法，一种是传递对象，另一种是发布的属性包装器。

## 图像加载器

让我们先看看 PassthroughtSubject 的实现，它是你可以在 GitHub
上找到的

```
class ImageLoader: ObservableObject {
    var didChange = PassthroughSubject<Data, Never>()
    var data = Data() {
        didSet {
            didChange.send(data)
        }
    }

    init(urlString:String) {
        guard let url = URL(string: urlString) else { return }
        let task = URLSession.shared.dataTask(with: url) { data, response, error in
            guard let data = data else { return }
            DispatchQueue.main.async {
                self.data = data
            }
        }
        task.resume()
    }
} 
```

我们需要设置一个数据变量，这就是我们正在加载的远程图像。我们可以用空数据初始化它，所以显然不是一个有效的图像。在我们通过 URLSession dataTask 获取远程数据后，我们可以更改数据，正如你在 didSet 上看到的，我们在 didChange 上调用 send，一个 PassthroughSubject。我们的 ImageView 将自己注册为该发布者的接收者，因此当我们设置数据变量时，接收者会得到通知并可以读取它，然后尝试从它创建一个 UIImage。
注意，可以使用。在与发布者打交道时，我们也可以在 init 函数上监听发布者。
如果你对出版商如何工作感兴趣，请参考我的其他文章[联合收割机第一个例子](http://www.gfrigerio.com/combine-first-example/)和[与联合收割机联网](http://www.gfrigerio.com/networking-example-with-combine/)。

## 使用@发布

还有第二种更简单的方法来实现 ImageLoader，以便将其用作 ObservedObject。在 GitHub 上你只能找到第一个实现，所以如果你喜欢另一个实现，你需要从这篇文章中复制并粘贴

```
class ImageLoader: ObservableObject {
    @Published var data:Data?

    init(urlString:String) {
        guard let url = URL(string: urlString) else { return }
        let task = URLSession.shared.dataTask(with: url) { data, response, error in
            guard let data = data else { return }
            DispatchQueue.main.async {
                self.data = data
            }
        }
        task.resume()
    }
} 
```

如您所见，数据仍然是我们想要更新的变量，但是通过使用@Published 属性包装器，我们不需要像 PassthroughSubject 那样创建自己的发布者并通知我们的订阅者。属性包装器会处理它。这是 ImageView 的替代版本。

```
var body: some View {
        VStack {
            Image(uiImage: imageLoader.data != nil ? UIImage(data:imageLoader.data!)! : UIImage())
                .resizable()
                .aspectRatio(contentMode: .fit)
                .frame(width:100, height:100)
        }
    } 
```

这里我们可以直接检查 imageLoader 的数据属性。如果它是 nil，我们使用一个空的 UIImage，否则我们可以使用数据创建一个 UIImage。设置数据后,@Published 将通知 ImageView，ImageView 将使用 imageLoader 中数据的更新版本重新加载图像。
我不喜欢测试 nil，所以第三种选择是在 ImageLoader 中用一个 bool 作为发布值，就像这样

```
class ImageLoader: ObservableObject {
    @Published var dataIsValid = false
    var data:Data?

    init(urlString:String) {
        guard let url = URL(string: urlString) else { return }
        let task = URLSession.shared.dataTask(with: url) { data, response, error in
            guard let data = data else { return }
            DispatchQueue.main.async {
                self.dataIsValid = true
                self.data = data
            }
        }
        task.resume()
    }
}

// ImageView

func imageFromData(_ data:Data) -> UIImage {
    UIImage(data: data) ?? UIImage()
}

var body: some View {
    VStack {
        Image(uiImage: imageLoader.dataIsValid ? imageFromData(imageLoader.data!) : UIImage())
            .resizable()
            .aspectRatio(contentMode: .fit)
            .frame(width:100, height:100)
    }
} 
```

就是这样，也许第二个实现更容易，也是最受欢迎的，但是我喜欢使用 publishers，所以我从使用 Combine 的那个开始，我认为它提供了更多的控制，即使这是一个简单的例子，而且使用 PassthroughSubject 感觉有些过头了。
编码快乐！