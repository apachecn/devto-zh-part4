# C#可视代码中的错误镜头扩展

> 原文：<https://dev.to/mteheran/error-lens-extension-in-visual-code-for-c-2p08>

Error lens 是视觉代码中的一个惊人的扩展，当你处理一个文件或打开一个新项目时，它会实时向你显示所有的错误和警告。

首先，澄清一下，这个扩展可用于许多编程语言和技术。即使您不是 C#开发人员，您也可以用 Javascript、Python 等来尝试这个扩展...

[![](img/c167c6b1789f58ac227247d9acdfa365.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--S1INpMsT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/i897dqjd0mz8eevohgs7.png)

安装误差透镜后，您只需打开或编辑一个文件，即可实时查看该扩展的效果和验证。

以下是 Error lens 在 C#中检测到的常见错误和警告列表

1.  不必要的使用:
    [![](img/7398399de3ce4e771e3ec07309d04d7c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HNWXUZaU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rh7ievx69r6cy8gwnaoq.png)

2.  行尾:
    [![](img/b55734844cd3071730d1c092fccdbf6b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1lyHtXkf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3wl27k8e3o8d3osfnwt0.png)

3.  名称空间缺失:
    [![](img/202115feb3ba941faedf4d21659a098b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mfUP1dfl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wdmfga63q9gezut89o1n.png)

4.  拼错:
    [![](img/dab38d76e507637c00c2f22b2038fb17.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Y2RAmL8q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/et1ek6g4u8v50r4yca6u.png)

误差镜头也有一个配置设置部分。您可以设置延迟来显示错误，并更改每种错误的颜色。

[![](img/b66e0e578c0bca4f0c1c7d60e81f7904.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QB_a6Ngj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zmkz91dow31krh9phmd7.png)

git 中的官方知识库:
[https://github.com/phindle/error-lens](https://github.com/phindle/error-lens)

试试吧！！