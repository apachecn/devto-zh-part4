# UnityのProBuilderをLWRP/HDRP 対応する

> 原文：<https://dev.to/shiena/unity-probuilder-lwrp-hdrp-fad>

# 前言

如果在 Unity 中设置为 LWRP/HDRP，然后在 ProBuilder 中创建游戏对象，则会因为使用的是传统着色器而变为粉红色。 但是，在 ProBuilder 4.1.0 或更高版本中添加了支持 LWRP/HDRP 的材质/着色器，下面介绍如何设置。

# 针对 LWRP/HDRP 的设定步骤

1.  从包管理器安装 ProBuilder 4.1.0 或更高版本。
2.  Unity2019.1 and above

    1.  按 HDRP Support 或 LWRP Support 上的 Import in project 按钮。 ![image.png](img/f55a26c86ab0cf275b173668b6bb9109.png)
    2.  将材质和着色器复制到`/Assets/Samples/ProBuilder/4.1.0/`。 ![image.png](img/d917790ae28b37471a0a8a9e7a2daaf2.png)
3.  Unity below Unity2018.4

    1.  Package Manager 不支持 Import in project 按钮，因此用资源管理器打开 Unity 的项目选项卡内的`/Packages/ProBuilder`，将`Samples~`文件夹内的`HDRP`或`LWRP`复制到项目内 ![image.png](img/a82535e9676d590f8ffe0d704e430d12.png)![image.png](img/5e2d5b58bb7c733d1e0a0bb036fbca4e.png)
4.  复制的材质在项目选项卡中的预览可能会变成粉红色，但在重新导入时会正常显示。

    请注意，材质可以位于项目中的任何位置，但如果更改了文件名，则不会自动指定材质。

在 ProBuilder 中使用以上设置创建游戏对象时，将自动指定 LWRP/HDRP 材质。

闲话不多说，材质自动指定是在[https://github.com/unity-technologies/com.unity.pro builder/pull/165](https://github.com/Unity-Technologies/com.unity.probuilder/pull/165)中引入的

[![image.png](img/be2ad57823aa920c9d2d9336ffb9274b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3IWadA-5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/mqhb2unxld03ys8bglxi.png)

# 低于 ProBuilder 4.1.0 时

1.  [https://github.com/unity-technologies/com.unity.pro builder/tree/v4.1.0/ samples ~](https://github.com/Unity-Technologies/com.unity.probuilder/tree/v4.1.0/Samples%7E)中添加了 4.
2.  在“Unity 环境设置”>“ProBuilder”>“Mesh Settings”>“Material”中设置复制的材质。 ![image.png](img/cae47462eaea1de2c3ea5a3bdcac4c89.png)