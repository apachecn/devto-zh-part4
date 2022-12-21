# Maven toolchains.xml 脚本

> 原文：<https://dev.to/allenball/maven-toolchains-xml-script-2lkm>

Apache Maven 引入了 Maven 工具链来简化插件的配置，并避免在项目 POM 中指定任何 JDK 位置。可用的 JDK 在`${HOME}/.m2/toolchains.xml`中配置。本文介绍了一个脚本来自动生成`toolchains.xml`文件`toolchains.xml.bash`。

## 论操作

对于每个已配置的 JDK，`toolchains.xml`文件包含一个`<toolchain/>`元素。

```
<?xml version="1.0" encoding="UTF-8"?>
<toolchains>
  ...
  <toolchain>
    <type>jdk</type>
    <provides>
      <version>VERSION</version>
      <vendor>VENDOR</vendor>
    </provides>
    <configuration>
      <jdkHome>JDKHOMEDIR</jdkHome>
    </configuration>
  </toolchain>
  ...
</toolchains> 
```

Enter fullscreen mode Exit fullscreen mode

对于每个`JDKHOMEDIR`候选者，`VERSION`由 JVM 系统属性`java.specification.version`指定，一致的供应商名称由`java.vendor`指定。这个简单的 Java 程序(兼容 Java 1.5 和更高版本)将提供模板。(JDK 目录必须作为命令行参数提供。)

```
public class ToolchainEntry {
    public static void main(String[] argv) {
        System.out
            .format("  <toolchain>\n")
            .format("    <type>jdk</type>\n")
            .format("    <provides>\n")
            .format("      <version>%s</version>\n",
                    System.getProperty("java.specification.version"))
            .format("      <vendor>%s</vendor>\n",
                    System.getProperty("java.vendor"))
            .format("    </provides>\n")
            .format("    <configuration>\n")
            .format("      <jdkHome>%s</jdkHome>\n", argv[0])
            .format("    </configuration>\n")
            .format("  </toolchain>\n");
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在下一节中，这个程序与一个`bash`脚本结合在一起。

## 脚本

一个`bash`脚本用于驱动上一节描述的 Java 程序的执行，其职责是:

1.  确定候选 JVM 目录:
    *   麦克·OS X(达尔文):`/Library/Java/JavaVirtualMachines/*.jdk/Contents/Home`
    *   Linux: `/usr/lib/jvm/*jdk*`
2.  创建`Toolchain.java`并编译为`Toolchain.class` <sup id="ref1">[1](#endnote1)</sup>
3.  创建一个新的`<toolchains/>`文档，并通过候选 JVM 执行`Toolchain.class`遍历每个候选 JDK 目录

```
#!/bin/bash

shopt -s nullglob
JDKS+=({/Library/Java/JavaVirtualMachines/*.jdk/Contents/Home,/usr/lib/jvm/*jdk*})

cat > ToolchainEntry.java <<EOF public class ToolchainEntry {
    public static void main(String[] argv) {
        System.out
            .format("  <toolchain>\n")
            .format("    <type>jdk</type>\n")
            .format("    <provides>\n")
            .format("      <version>%s</version>\n",
                    System.getProperty("java.specification.version"))
            .format("      <vendor>%s</vendor>\n",
                    System.getProperty("java.vendor"))
            .format("    </provides>\n")
            .format("    <configuration>\n")
            .format("      <jdkHome>%s</jdkHome>\n", argv[0])
            .format("    </configuration>\n")
            .format("  </toolchain>\n");
    }
} EOF javac -Xlint:none -source 1.7 -target 1.7 ToolchainEntry.java

echo '<?xml version="1.0" encoding="UTF-8"?>' > toolchains.xml
echo '<toolchains>' >> toolchains.xml

for jdk in ${JDKS[@]}; do
    if [ -d ${jdk} ]; then java=${jdk}/bin/java

        if [ -x ${java} ]; then
            ${java} ToolchainEntry ${jdk} >> toolchains.xml
        fi
    fi
done echo '</toolchains>' >> toolchains.xml

rm -rf ToolchainEntry.* 
```

Enter fullscreen mode Exit fullscreen mode

## 输出

下面提供了为`Mac OS X`和`Linux` (Google Cloud Shell)生成的`toolchains.xml`文件示例。

```
<?xml version="1.0" encoding="UTF-8"?>
<toolchains>
  <toolchain>
    <type>jdk</type>
    <provides>
      <version>10</version>
      <vendor>AdoptOpenJDK</vendor>
    </provides>
    <configuration>
      <jdkHome>/Library/Java/JavaVirtualMachines/adoptopenjdk-10.jdk/Contents/Home</jdkHome>
    </configuration>
  </toolchain>
  <toolchain>
    <type>jdk</type>
    <provides>
      <version>11</version>
      <vendor>AdoptOpenJDK</vendor>
    </provides>
    <configuration>
      <jdkHome>/Library/Java/JavaVirtualMachines/adoptopenjdk-11.jdk/Contents/Home</jdkHome>
    </configuration>
  </toolchain>
  <toolchain>
    <type>jdk</type>
    <provides>
      <version>12</version>
      <vendor>AdoptOpenJDK</vendor>
    </provides>
    <configuration>
      <jdkHome>/Library/Java/JavaVirtualMachines/adoptopenjdk-12.jdk/Contents/Home</jdkHome>
    </configuration>
  </toolchain>
  <toolchain>
    <type>jdk</type>
    <provides>
      <version>1.8</version>
      <vendor>AdoptOpenJDK</vendor>
    </provides>
    <configuration>
      <jdkHome>/Library/Java/JavaVirtualMachines/adoptopenjdk-8.jdk/Contents/Home</jdkHome>
    </configuration>
  </toolchain>
  <toolchain>
    <type>jdk</type>
    <provides>
      <version>9</version>
      <vendor>Oracle Corporation</vendor>
    </provides>
    <configuration>
      <jdkHome>/Library/Java/JavaVirtualMachines/adoptopenjdk-9.jdk/Contents/Home</jdkHome>
    </configuration>
  </toolchain>
  <toolchain>
    <type>jdk</type>
    <provides>
      <version>11</version>
      <vendor>Amazon.com Inc.</vendor>
    </provides>
    <configuration>
      <jdkHome>/Library/Java/JavaVirtualMachines/amazon-corretto-11.jdk/Contents/Home</jdkHome>
    </configuration>
  </toolchain>
  <toolchain>
    <type>jdk</type>
    <provides>
      <version>12</version>
      <vendor>Oracle Corporation</vendor>
    </provides>
    <configuration>
      <jdkHome>/Library/Java/JavaVirtualMachines/jdk-12.0.2.jdk/Contents/Home</jdkHome>
    </configuration>
  </toolchain>
  <toolchain>
    <type>jdk</type>
    <provides>
      <version>1.7</version>
      <vendor>Oracle Corporation</vendor>
    </provides>
    <configuration>
      <jdkHome>/Library/Java/JavaVirtualMachines/jdk1.7.0_80.jdk/Contents/Home</jdkHome>
    </configuration>
  </toolchain>
  <toolchain>
    <type>jdk</type>
    <provides>
      <version>1.8</version>
      <vendor>Oracle Corporation</vendor>
    </provides>
    <configuration>
      <jdkHome>/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home</jdkHome>
    </configuration>
  </toolchain>
  <toolchain>
    <type>jdk</type>
    <provides>
      <version>1.8</version>
      <vendor>Oracle Corporation</vendor>
    </provides>
    <configuration>
      <jdkHome>/Library/Java/JavaVirtualMachines/jdk1.8.0_202.jdk/Contents/Home</jdkHome>
    </configuration>
  </toolchain>
</toolchains> 
```

Enter fullscreen mode Exit fullscreen mode

```
<?xml version="1.0" encoding="UTF-8"?>
<toolchains>
  <toolchain>
    <type>jdk</type>
    <provides>
      <version>11</version>
      <vendor>Debian</vendor>
    </provides>
    <configuration>
      <jdkHome>/usr/lib/jvm/java-1.11.0-openjdk-amd64</jdkHome>
    </configuration>
  </toolchain>
  <toolchain>
    <type>jdk</type>
    <provides>
      <version>11</version>
      <vendor>Debian</vendor>
    </provides>
    <configuration>
      <jdkHome>/usr/lib/jvm/java-11-openjdk-amd64</jdkHome>
    </configuration>
  </toolchain>
  <toolchain>
    <type>jdk</type>
    <provides>
      <version>1.8</version>
      <vendor>Oracle Corporation</vendor>
    </provides>
    <configuration>
      <jdkHome>/usr/lib/jvm/java-1.8.0-openjdk-amd64</jdkHome>
    </configuration>
  </toolchain>
  <toolchain>
    <type>jdk</type>
    <provides>
      <version>1.8</version>
      <vendor>Oracle Corporation</vendor>
    </provides>
    <configuration>
      <jdkHome>/usr/lib/jvm/java-8-openjdk-amd64</jdkHome>
    </configuration>
  </toolchain>
</toolchains> 
```

Enter fullscreen mode Exit fullscreen mode

**【1】**即使 java 源码支持的最早 Java 版本是 1.5，Oracle 的 JDK 12 能产生的最早代码也是针对 1.7 的。
[↩](#ref1)