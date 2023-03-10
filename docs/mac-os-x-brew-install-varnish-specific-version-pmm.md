# Mac OS X brew 安装清漆特定版本

> 原文：<https://dev.to/asrar7787/mac-os-x-brew-install-varnish-specific-version-pmm>

在 Mac OS X High Sierra 或更高版本中，假设您使用 *brew* 将 Varnish HTTP Accelerator 从 4.x.x/5.x.x 升级到任何最新版本 6.x.x。现在，出于任何原因，您想回到以前的版本，即 5.2.0 吗？好消息，伙计们！
你可以在本地复制实际的 brew 配方，并在 *rb* 文件中定义你想要的版本。
*在下面的例子中，我想从 6.2.0 版本恢复到 5.2.0 版本。*

让我们将文件从[https://github . com/home brew/home brew-core/blob/master/Formula/varnish . Rb](https://github.com/Homebrew/homebrew-core/blob/master/Formula/varnish.rb)复制到您的本地。然后编辑 *varnish.rb* 文件，删除/注释掉以下几行:

```
sha256 "c37af353aca25a83d22f9c5ce0ae800fe433e4d02e1457e02886a5849f988e53"

bottle do

    sha256 "7101ff2895f77803ea1e1ca6c8ebc1feef2c07b77fbf612f6dcf6983bf4ffc27" => :mojave

    sha256 "d1fd8751be2ddccb463b385377b88cc440632eb1a07c0175fab9ee77c57f986d" => :high_sierra

    sha256 "e124ad1c5638c7304743b5f3dbec996afa33c972dbe991ec35280c4d7787b3a4" => :sierra

end 
```

替换此行:

```
url "https://varnish-cache.org/_downloads/varnish-6.2.0.tgz" 
```

随着

```
url "https://varnish-cache.org/_downloads/varnish-5.2.0.tgz" 
```

你可以在 https://varnish-cache.org/releases/得到所有可用的版本。你所需要做的就是点击想要的版本，然后粘贴下载网址。

现在，保存这个 *varnish.rb* 文件并运行:

```
chmod 775 varnish.rb
brew install ./varnish.rb 
```

brew 将执行此操作，并按预期安装 varnish 5.2.0。

仅此而已！