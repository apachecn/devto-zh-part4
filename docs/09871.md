# 将代码格式带入传统。

> 原文：<https://dev.to/saifali40/bringing-the-code-format-to-legacy-3cen>

一个没有遗留代码的项目就像不编码的开发人员的存在一样。

你今天可能写了一个干净的代码，但有时它会成为遗留代码，所以我们不能在我们的领域中摆脱遗留代码。

现在让我们开始重点，我们有一个项目开始于 2011 年，一些代码甚至没有改变，直到日期，所以我被指派将整个代码库格式化为 [google style](https://google.github.io/styleguide/javaguide.html) 我尝试了以下 bash 函数来找出每个文件的最后更新日期。

```
function ghlbt {
         git ls-tree -r --name-only HEAD |
        while read filename  ;
        do
            if
                [[ $filename == *.$1 ]] ; then echo " $filename ," $(git log -1 --format="%ad, %an" -- $filename);
            fi  ;
        done >> $1_$2.csv
} 
```

`please replace $1 with the extension and $2 with the directory name or whatever suffix you need`

我们得到了一个 CSV 文件和订单的更新日期。你也可以改变这一点，你可以用谷歌搜索其他模式。

我们想先清理旧的文件，但在生成 CSV 文件后，我意识到我们有 1200 多个 java 文件，格式化所有文件并不是一个好主意，所以我们想利用我们的 Jenkins 来做这件事，并使这个过程自动化。

我们用 Gradle 做建筑项目。幸运的是，Gradle 有可用的格式化程序。

```
buildscript {
  repositories {
    mavenCentral()
    jcenter {
    }
  }

dependencies {
    classpath "gradle.plugin.com.github.sherter.google-java-format:google-java-format-gradle-plugin:0.8"
  }
} 
```

将以下内容添加到插件
`apply plugin: "com.github.sherter.google-java-format"`

所以在格拉德的工作已经完成了。

在 jenkins 中，我们使用常规的 git 注释，在提交钩子到生产分支之后，从那里我们创建一个中间分支，格式化代码并在格式化之后提交给主分支。

第 2 部分将讨论 jenkins 管道代码。