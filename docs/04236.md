# 在 Unity 中修补 Package Manager 提供的软件包

> 原文：<https://dev.to/shiena/unity-package-manager-2p32>

# 前言

在 Unity2019.2.3f1 中使用 Shader Graph 6.9.1 时，重命名特性名称会冻结。
调查是否被修正，果然[[ 6.9.x ] fix input view freeze by alindmanunity·pull request # 4386·unity-technologies/schnologies
因此，我们研究了如何修补 Pakcage Manager 提供的软件包以修复 ShaderGraph。](https://github.com/Unity-Technologies/ScriptableRenderPipeline/pull/4386)

# 项目固有的情况

从 Package Manager 安装软件包时，将在以下文件夹中创建缓存:

```
[Project Folder]/Library/PackageCache/* 
```

从这里找到相应的软件包，将其复制到以下文件夹并打补丁。
复制目标文件夹优先于缓存，所以这样就可以了。

```
[Project Folder]/Packages/ 
```

发布了错误修复版，从 Package Manager 更新了版本后，就不需要打了补丁的文件夹了，所以删除吧。

# 只有特定的 PC 的情况

从 Package Manager 安装软件包时，缓存将下载到以下文件夹中，并被复制到 Unity 项目中:

窗户

```
%LOCALAPPDATA%\Unity\cache\packages\packages.unity.com\* 
```

苹果电脑

```
~/Library/Unity/cache/packages/packages.unity.com/* 
```

更改下载的包时，Unity 项目也会反映缓存。
但是多人共享 Unity 项目时，不会反映在其他 PC 上，所以不太推荐使用该方法。