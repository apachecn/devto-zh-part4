# 如果使用 mvn 和 testng 得到一个随机的无用错误

> 原文：<https://dev.to/tonetheman/if-you-get-a-random-useless-error-using-mvn-and-testng-about-certs-2cf8>

这是我得到的错误

```
There was an error in the forked process
sun.security.provider.certpath.SunCertPathBuilderException: unable to find valid certification path to requested target
org.apache.maven.surefire.booter.SurefireBooterForkException: There was an error in the forked process
sun.security.provider.certpath.SunCertPathBuilderException: unable to find valid certification path to requested target
... 
```

Enter fullscreen mode Exit fullscreen mode

当试图使用 maven 和 surefire 运行 testng 测试时，这是非常没有帮助的。

为了解决这个问题，我修改了 testng.xml 文件中的 xml 头。叹气。

这种东西没有理由。Java 编程的某些部分会引发火灾，这就是一个很好的例子。xml 文件中的文本绝不会导致这样的错误。仅仅是 Java/testng/maven/surefire 方面的彻底失败？链条中的某个人实际上并不希望你使用他们的产品...谁知道失败在哪里。

我把在 testng 网站上找到的标题改成了这个

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE suite SYSTEM "http://testng.org/testng-1.0.dtd" > 
```

Enter fullscreen mode Exit fullscreen mode

不再有错误。:(