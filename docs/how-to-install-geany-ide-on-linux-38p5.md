# 如何在 Linux 上安装 Geany IDE

> 原文：<https://dev.to/xeroxism/how-to-install-geany-ide-on-linux-38p5>

<figure>[![geany screenshot 4](img/4ba6ddff88855ff0f71a880f6b7b58b6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--s_dsh0Sp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/fossnaija.com/wp-content/uploads/2019/07/geany-screenshot-1.png%3Fw%3D665%26ssl%3D1)

<figcaption>【geany ide】</figcaption>

</figure>

Geany 是一个跨平台、功能丰富的成熟集成开发环境(IDE)，你可以在 T2 的 Linux 上运行。我喜欢使用 Geany IDE 的一个很好的原因是因为 automatica 编译器识别；一旦一种编程语言的编译器被安装在 PC 上，你不需要任何进一步的配置就可以开始使用该编译器编写程序。Geany 会自动为您在系统中找到编译器。因此，如果您需要一个非常轻量级的 IDE 来完成大部分编程(尤其是需要编译源代码的语言)，我会建议您选择 Geany。

<figure>[![geany_theme_screenshot_light](img/c91da1d13c12a2a0231496209ef0bc6a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--w1pksLLK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/fossnaija.com/wp-content/uploads/2019/07/geany_theme_screenshot_light.png%3Fresize%3D665%252C360%26ssl%3D1) 

<figcaption>吉亚尼灯光主题</figcaption>

</figure>

您应该尝试 geany 的其他原因包括但不限于以下几点:

*   **是轻量级的**。这非常重要，因为它不需要大量的系统资源(如内存)。它加载速度超快。虽然可以下载更多的插件来扩展功能，但是它只打包了必要的插件。

*   它可以用于简单的文本编辑，并且由于支持大量文件，可以作为一个有能力的 IDE 用于任何编程目的。

*   Geany 会自动保存和刷新会话。成交后，您可以从停止工作的地方继续。

*   **人气**。它常见于不同的 [Linux 发行版](https://fossnaija.com/10-common-terms-every-linux-user-should-know/)(发行版)的软件库中。

*   **是“免费”**。两个[都是自由](https://dev.to/xeroxism/why-hackathons-should-insist-on-free-software-8f1)(你可以分享修改和分享)和“免费啤酒”你可以免费下载和安装。

## -它支持许多流行的编程语言，如 JAVA、Python、Go、JavaScript、C/C++、Lisp、Pascal、[、Fortran](https://dev.to/xeroxism/how-to-install-fortran-compiler-in-linux-428l) 等等。

<figure>[![geany_screenshot_dark](img/d9c86bdbc82a609f18ed4185dd46536c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rDgnolXi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/fossnaija.com/wp-content/uploads/2019/07/geany_screenshot_dark.png%3Fresize%3D665%252C379%26ssl%3D1) 

<figcaption>Geany 黑暗主题。</figcaption>

</figure>

**安装**

Geany 可以在许多 Linux [ditros](https://fossnaija.com/10-common-terms-every-linux-user-should-know/) 的软件中心和包档案中获得。只需搜索“geany”并点击安装即可。

<figure>[![geany installation software centre](img/b664e961a1282e947e92cdf3c3ed0920.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FpBb7FuJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/fossnaija.com/wp-content/uploads/2019/07/geany-installation-software-centre.png%3Fresize%3D665%252C248%26ssl%3D1) 

<figcaption>吉亚尼安装。</figcaption>

</figure>

**使用命令行**

您可以使用以下命令组合在不同的 Linux 发行版中安装 geany

### **Debian/Ubuntu:**

**

```
sudo apt-get install geany 
```

**

要获得 geany 的新版本，您可以使用命令；

将*[_ PPA*]([https://fosnaija . com/how-to-install-software-using-PPA-Ubuntu/](https://fossnaija.com/how-to-install-software-using-ppa-ubuntu/))添加到您的系统中 _

**

```
sudo add-apt-repository ppa:geany-dev/ppa 
```

**

*更新系统软件档案；*

**

```
sudo apt-get update 
```

**

*安装前；*

**

```
sudo apt-get install geany 
```

**

### **Fedora Linux**

**

```
yum install geany 
```

**

### **Arch Linux**

```
pacman -S geany 
```

### **使用软件中心**

您也可以使用特定 Linux 发行版的软件中心来安装 geany 只需在搜索框中输入名字“geany ”,然后点击回车。从那里你可以安装一个简单的点击 geany。

您可以马上开始使用 Linux 上最好的 ide 之一。

快乐的 Linux！

如果你喜欢我们的文章，请分享。 [![🙂](img/1bb31e891282bfa40812655c9c9ace9e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DvXCaIjD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s.w.oimg/core/emoji/11/72x72/1f642.png) [脸书](https://www.facebook.com/fossnaija/)[推特](https://twitter.com/fossnaija/)[insta gram](https://www.instagram.com/fossnaija/)。

帖子[如何在 Linux 上安装 Geany IDE](https://fossnaija.com/install-geany-linux/)最早出现在 [Foss Naija](https://fossnaija.com) 上。