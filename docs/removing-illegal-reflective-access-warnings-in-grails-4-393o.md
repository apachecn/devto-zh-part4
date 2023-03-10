# 在 Grails 4 中移除非法反射访问警告

> 原文：<https://dev.to/erichelgeson/removing-illegal-reflective-access-warnings-in-grails-4-393o>

当在 Grails 4 中使用 JDK11 时，您可能会看到这些消息:

```
WARNING: An illegal reflective access operation has occurred
WARNING: Illegal reflective access by org.codehaus.groovy.reflection.CachedConstructor$1 (file:/Users/erichelgeson/.gradle/caches/modules-2/files-2.1/org.codehaus.groovy/groovy/2.5.6/6936e700f0fb1b50bac0698ada4347a769d40199/groovy-2.5.6.jar) to constructor com.sun.jmx.mbeanserver.JmxMBeanServer(java.lang.String,javax.management.MBeanServer,javax.management.MBeanServerDelegate)
WARNING: Please consider reporting this to the maintainers of org.codehaus.groovy.reflection.CachedConstructor$1
WARNING: Use --illegal-access=warn to enable warnings of further illegal reflective access operations
WARNING: All illegal access operations will be denied in a future release 
```

这是因为 Groovy 2.5 使用了反射。在 Groovy 3 中，他们已经在这个 T1 上取得了很大的进步。

现在，我们可以将正确的包添加到`--add-opens` JVM 参数中来删除它们。

**注意:**这与 Groovy 2.5 用 [`GROOVY_TURN_OFF_JAVA_WARNINGS`](https://github.com/apache/groovy/commit/a7f7cdd352a40dd7acd2def999eb560c2b76904e) 环境变量调用`groovy`时删除它们的方式相同。

在我们的`build.gradle`文件中，我们可以定义要打开的模块列表:

```
def jvmOpenModulesArgs = [
        "--add-opens=java.management/com.sun.jmx.mbeanserver=ALL-UNNAMED",
        "--add-opens=java.base/java.io=ALL-UNNAMED",
        "--add-opens=java.base/java.lang=ALL-UNNAMED",
        "--add-opens=java.base/java.lang.annotation=ALL-UNNAMED",
        "--add-opens=java.base/java.lang.invoke=ALL-UNNAMED",
        "--add-opens=java.base/java.lang.module=ALL-UNNAMED",
        "--add-opens=java.base/java.lang.ref=ALL-UNNAMED",
        "--add-opens=java.base/java.lang.reflect=ALL-UNNAMED",
        "--add-opens=java.base/java.math=ALL-UNNAMED",
        "--add-opens=java.base/java.net=ALL-UNNAMED",
        "--add-opens=java.base/java.net.spi=ALL-UNNAMED",
        "--add-opens=java.base/java.nio=ALL-UNNAMED",
        "--add-opens=java.base/java.nio.channels=ALL-UNNAMED",
        "--add-opens=java.base/java.nio.channels.spi=ALL-UNNAMED",
        "--add-opens=java.base/java.nio.charset=ALL-UNNAMED",
        "--add-opens=java.base/java.nio.charset.spi=ALL-UNNAMED",
        "--add-opens=java.base/java.nio.file=ALL-UNNAMED",
        "--add-opens=java.base/java.nio.file.attribute=ALL-UNNAMED",
        "--add-opens=java.base/java.nio.file.spi=ALL-UNNAMED",
        "--add-opens=java.base/java.security=ALL-UNNAMED",
        "--add-opens=java.base/java.security.acl=ALL-UNNAMED",
        "--add-opens=java.base/java.security.cert=ALL-UNNAMED",
        "--add-opens=java.base/java.security.interfaces=ALL-UNNAMED",
        "--add-opens=java.base/java.security.spec=ALL-UNNAMED",
        "--add-opens=java.base/java.text=ALL-UNNAMED",
        "--add-opens=java.base/java.text.spi=ALL-UNNAMED",
        "--add-opens=java.base/java.time=ALL-UNNAMED",
        "--add-opens=java.base/java.time.chrono=ALL-UNNAMED",
        "--add-opens=java.base/java.time.format=ALL-UNNAMED",
        "--add-opens=java.base/java.time.temporal=ALL-UNNAMED",
        "--add-opens=java.base/java.time.zone=ALL-UNNAMED",
        "--add-opens=java.base/java.util=ALL-UNNAMED",
        "--add-opens=java.base/java.util.concurrent=ALL-UNNAMED",
        "--add-opens=java.base/java.util.concurrent.atomic=ALL-UNNAMED",
        "--add-opens=java.base/java.util.concurrent.locks=ALL-UNNAMED",
        "--add-opens=java.base/java.util.function=ALL-UNNAMED",
        "--add-opens=java.base/java.util.jar=ALL-UNNAMED",
        "--add-opens=java.base/java.util.regex=ALL-UNNAMED",
        "--add-opens=java.base/java.util.spi=ALL-UNNAMED",
        "--add-opens=java.base/java.util.stream=ALL-UNNAMED",
        "--add-opens=java.base/java.util.zip=ALL-UNNAMED",
        "--add-opens=java.datatransfer/java.awt.datatransfer=ALL-UNNAMED",
        "--add-opens=java.desktop/java.applet=ALL-UNNAMED",
        "--add-opens=java.desktop/java.awt=ALL-UNNAMED",
        "--add-opens=java.desktop/java.awt.color=ALL-UNNAMED",
        "--add-opens=java.desktop/java.awt.desktop=ALL-UNNAMED",
        "--add-opens=java.desktop/java.awt.dnd=ALL-UNNAMED",
        "--add-opens=java.desktop/java.awt.dnd.peer=ALL-UNNAMED",
        "--add-opens=java.desktop/java.awt.event=ALL-UNNAMED",
        "--add-opens=java.desktop/java.awt.font=ALL-UNNAMED",
        "--add-opens=java.desktop/java.awt.geom=ALL-UNNAMED",
        "--add-opens=java.desktop/java.awt.im=ALL-UNNAMED",
        "--add-opens=java.desktop/java.awt.im.spi=ALL-UNNAMED",
        "--add-opens=java.desktop/java.awt.image=ALL-UNNAMED",
        "--add-opens=java.desktop/java.awt.image.renderable=ALL-UNNAMED",
        "--add-opens=java.desktop/java.awt.peer=ALL-UNNAMED",
        "--add-opens=java.desktop/java.awt.print=ALL-UNNAMED",
        "--add-opens=java.desktop/java.beans=ALL-UNNAMED",
        "--add-opens=java.desktop/java.beans.beancontext=ALL-UNNAMED",
        "--add-opens=java.instrument/java.lang.instrument=ALL-UNNAMED",
        "--add-opens=java.logging/java.util.logging=ALL-UNNAMED",
        "--add-opens=java.management/java.lang.management=ALL-UNNAMED",
        "--add-opens=java.prefs/java.util.prefs=ALL-UNNAMED",
        "--add-opens=java.rmi/java.rmi=ALL-UNNAMED",
        "--add-opens=java.rmi/java.rmi.activation=ALL-UNNAMED",
        "--add-opens=java.rmi/java.rmi.dgc=ALL-UNNAMED",
        "--add-opens=java.rmi/java.rmi.registry=ALL-UNNAMED",
        "--add-opens=java.rmi/java.rmi.server=ALL-UNNAMED",
        "--add-opens=java.sql/java.sql=ALL-UNNAMED"
] 
```

然后将它们添加到我们的`bootRun`任务:

```
bootRun {
    jvmArgs('-noverify',
            '-XX:TieredStopAtLevel=1',
            '-Xmx1024m')
    jvmArgs(jvmOpenModulesArgs)
    // ...
} 
```

而任何任务都带有`GroovyCompile` & `Test`

```
tasks.withType(GroovyCompile) {
    options.forkOptions.jvmArgs = jvmOpenModulesArgs
}
tasks.withType(Test) {
    jvmArgs = jvmOpenModulesArgs
} 
```

就是这样 JVM 启动时不再有讨厌的反射访问警告。感谢 Daniel Sun 帮助我找到合适的模块。