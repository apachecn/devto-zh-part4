# 分享！

> 原文：<https://dev.to/disjfa/share-4gb>

在互联网上，我们分享东西，脸书，推特，电子邮件，其他社交，等等。我们使用一些链接自定义共享链接，并建立自己。当我开发一些我也分享的应用程序时，通常会有分享功能。现在，如果我们想以一种简单的方式实现 share，让我们看看我们能做些什么。

### 共享 api。

像往常一样，互联网的人们已经想到了这一点。当我搜索一个共享 api 时，当然有一个。 [Navigator.share](https://developer.mozilla.org/en-US/docs/Web/API/Navigator/share) api。太棒了。

这将我们设置为使用你在手机上看到的默认共享滑块来共享链接，其中只有你在手机或设备上设置的共享类型。有用又干净！

### 让我们实现。

接下来，一个简单的实现。接下来，我们需要一个页面上的按钮。

```
<a href="#" class="btn btn-outline-secondary js-share" data-title="This is a title" data-text="This is a text" aria-label="Share this page">
    <i class="fa fa-share-alt"></i>
</a> 
```

酷，分享按钮使用一些字体真棒分享图标！让我们添加一些 JavaScript。

```
document.addEventListener('click', (evt) => {
  const share = evt.target.closest('.js-share');
  if (!share) {
    return;
  }

  if (!share.dataset.title) {
    return;
  }

  navigator.share({
    title: share.dataset.title,
    text: share.dataset.text,
    url: share.dataset.url || location.href
  });

  evt.preventDefault();
}); 
```

创建了一个默认的点击监听器，监听一个应该被点击的`.js-share`类。注意，这使用了一个令人讨厌的`.closest`函数。更多的解释和我从哪里了解到的可以在克里斯费迪南德的网站找到[。](https://gomakethings.com/checking-event-target-selectors-with-event-bubbling-in-vanilla-javascript/)

然后运行代码！我在一个桌面上疯狂点击，想看看它是如何工作的。但是收效甚微。悲哀。

### 我们可以用那个！

所以下一步是询问互联网我们是否可以使用它。[我能使用 web 共享 api](https://caniuse.com/#feat=web-share) 吗？答案是不，还没有。不是开箱即用的内置产品。如前所述，share api 使用手机上使用的共享对话框。caniuse.com[上的图表仅在这一点上显示了移动浏览器。我在某处读到 macos 可能会在某个时候实现某些东西。但还没有。所以要不要用这个，是的！](https://caniuse.com)

### 让多边形填充。

我在网上问了更多，发现更多的人想实现这一点。所以我偶然发现了 [share-api-polyfill](https://github.com/NascHQ/share-api-polyfill) 。这将添加一个回退，就像我们在桌面上使用的一样，并实现与上面相同的结构。因此，只需将 JavaScript 导入到您的站点，您就可以开始工作了。

```
<script src="https://unpkg.com/share-api-polyfill/dist/share-min.js"></script> 
```

或者使用 npm 安装并在您的 JavaScript 中构建它。

```
npm install share-api-polyfill --save 
```

### 搞定！

不错！现在，我们有了一个功能，可以让每一种浏览应用的方式都可以很好地分享。尽管浏览器将实现 api 共享，但它将比以往任何时候都更好。在 share poly fill 中，您还可以设置一些东西，供自己休闲使用。

### 接下来

下一个。在 poly fill 的例子中，share api 返回一个承诺。所以你也可以扩展它来增加更多的共享功能。感谢分享或记录你的数据收集工作的人。

```
navigator.share({
    ...
})
.then( _ => console.log('Yay, you shared it :)'))
.catch( error => console.log('Oh noh! You couldn\'t share it! :\'(\n', error)); 
```

谷歌也在以这种方式分享文件，你可以查看谷歌页面上的[分享文件和网络分享](https://developers.google.com/web/updates/2019/05/web-share-files)帖子。作为一个想法，分享一个文件到一个图像编辑网站会很棒！

接下来就是我们想要的了。

尽情享受吧！

凯尔·格伦在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

[原文](https://disjfa.github.io/javascript/2019/07/02/share.html)