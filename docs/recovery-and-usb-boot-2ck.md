# 恢复和 USB 启动

> 原文：<https://dev.to/th3n00bc0d3r/recovery-and-usb-boot-2ck>

现在我们将全新安装 MacOS，因此我们需要来自 Apple 的映像，然后我们将创建一个 USB 来引导它。

打开 App Store，搜索 Mojave。

[![Alt Text](img/6a25bb0340ae508bd5bbda323ba736d5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OQHCkgUG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/n1c7f2u7znwsl2mpge2r.jpg)

点击 Oo 获取，它应该开始下载它。

[![Alt Text](img/b4aaf3333b5b0d3f8dc6290060a545f2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WdMLcQph--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/l9f0hthqjuoeaa3jj6oe.jpg)

我的，带我到更新部分，并开始下载图片。

[![Alt Text](img/4532399c9d5f666ee1d033669bf69b0e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nxugU3DS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9vqdc9ysgto17w483lkk.jpg)

完成后，将 USB 连接到 Mac 并打开“磁盘工具”。

[![Alt Text](img/94afb8e806885dd570f507ad1e475205.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--X6-l-vru--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/oyw867rijcb0hfs4sxk3.jpg)

现在选择 USB 并点击擦除分区和名称是 USB

[![Alt Text](img/43f448085538d6506c44719585d34a50.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1seHQa3v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0lw2rnrhbaq66huvpx04.jpg)

[![Alt Text](img/dfd17a7ddb8e4b01a7030cafe43b23cf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1sNgUij_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xsw6tl2797fc25cwqa8b.jpg)

[![Alt Text](img/173a9a44d10ceea73216b94f56adc76e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Uaq9KyFf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/w4gtx78tmr2qx50tr492.jpg)

接下来打开终端并粘贴以下命令

```
sudo /Applications/Install\ macOS\ Mojave.app/Contents/Resources/createinstallmedia --volume /Volumes/USB--nointeraction && say Mojave Drive Created 
```

Enter fullscreen mode Exit fullscreen mode

[![Alt Text](img/a3bed98d996e249f2b4688c2672db336.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AOvWLJ4e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5u2fdozc3yu9vq7n8cw3.jpg)

[![Alt Text](img/3bafbe55eb444f99b7fac1cd90993885.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OtsUFl1o--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pwlc3stakj81t8ekt0xj.jpg)

完成后，您就可以使用 USB 了。