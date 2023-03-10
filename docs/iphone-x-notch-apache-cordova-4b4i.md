# Iphone X Notch -阿帕奇科尔多瓦

> 原文：<https://dev.to/ecudevs/iphone-x-notch-apache-cordova-4b4i>

# Arreglar Iphone X Notch-Apache Cordova

对于许多人来说，处理苹果上市的新手机的 notch 是一种头痛。

[![](img/c3517a4dc0151711b1cfb64bc427c1b8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qEL0PPht--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vuuatt1j1be3eycsdurx.png) 
我们考虑以下模拟器:

Iphone X
`cordova run ios -l --target="iPhone-X, 12.2" --buildFlag="-UseModernBuildSystem=0"`

Iphone SE
`cordova run ios -l --target="iPhone-SE, 12.2" --buildFlag="-UseModernBuildSystem=0"`

## 我们开始了

1.  变更行动装置工具列/状态列的颜色。
    修改项目根目录的 config.xml 文件。
    [![](img/feee966b4ef5e3119af3091defb2a22c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nDhGHS2f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7ld1mot5dda8av9qsx3o.png) 
    修改以下行并用十六进制替代颜色(UI:使用应用程序的主色调)
    `<preference name="StatusBarBackgroundColor" value="#005eb8"/>`

2.  我们在元标记部分
    `<meta name="viewport"
    content="width=device-width, initial-scale=1.0, minimum-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">`的 index.html 中添加了以下行

[![](img/638a727bdb7732f711a5d8a33ebea044.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tTOmYV9---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qt70r4s6kdfy771lo9m4.png)

还不是全部，我们必须修改我们的欢迎画面

1.  我们创建了大小为 2732 px x 2732 px(ipads 最大尺寸)的闪屏。我们尽可能专注于设计。
    我们将其命名为:default @ 2x ~ universal ~ any . png

2.  我们将闪屏添加到我们的 config.xml 文件
    `<splash src="resources/ios/splash/Default@2x~universal~anyany.png" />`
    注意:它必须在`<platform name="ios">`内