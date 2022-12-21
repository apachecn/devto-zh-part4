# 不要让 shadow jar 插件遮蔽你的应用程序

> 原文：<https://dev.to/viniciusccarvalho/don-t-let-the-shadow-jar-plugin-shadow-your-application-3i5b>

在我的[上一篇](https://dev.to/viniciusccarvalho/deploying-fatjar-ktor-applications-on-google-cloud-app-engine-1o32)文章中，我分享了一个将 [ktor](https://ktor.io) 应用程序部署到 Google App Engine 的模板。

作为这个过程的一部分，我使用了 [Shadow](https://imperceptiblethoughts.com/shadow/) 插件。
事实证明，默认情况下插件不会从 java [Serviceloader](https://docs.oracle.com/javase/9/docs/api/java/util/ServiceLoader.html) 中复制服务文件。

这导致我的应用程序由于没有加载 SPI 类而失败。因此，如果你的应用程序因使用插件而中断，只需将其添加到你的`build.gradle`

```
shadowJar {
    mergeServiceFiles()
} 
```

快乐编码