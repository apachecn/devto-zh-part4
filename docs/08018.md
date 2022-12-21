# 新颤振项目中的包名

> 原文：<https://dev.to/cadoangelus/package-name-in-a-new-flutter-project-3jml>

如果你像我一样使用 Visual Studio 代码来处理 Flutter，你很可能会使用 Flutter CLI 来创建新的 Flutter 项目。

默认情况下，Flutter CLI 将使用`com.example.project_name`作为您的包名/包标识符。如果你想指定改变这种行为，你可以运行

`flutter create --org com.my_cool_org project_name`

这会将 Android 包名设置为`com.my_cool_org.project_name`，将 iOS 包标识符设置为`com.myCoolOrg.projectName`