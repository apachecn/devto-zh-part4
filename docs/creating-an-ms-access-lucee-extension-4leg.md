# 创建 MS Access Lucee 扩展

> 原文：<https://dev.to/mikeborn/creating-an-ms-access-lucee-extension-4leg>

我将一个简单的任务(从 MS Access 数据库迁移数据)转化为一个 Lucee 扩展，该扩展封装了 UCanAccess JDBC 驱动程序。

## 目录

*   [查询来自 CFML 的访问文件](#Querying-an-Access-File-from-CFML)
*   JDBC 太棒了！
*   为什么是 Lucee 扩展？
*   [创建 Lucee 扩展](#Creating-the-Lucee-Extension)
*   [用 CommandBox 和 ForgeBox 封装 Lucee 扩展](#Packaging-a-Lucee-Extension-with-CommandBox-and-ForgeBox)
*   [使用 UCanAccess Lucee 扩展](#Using-the-UCanAccess-Lucee-Extension)
*   [艰难地连接到 MS Access](#Connecting-To-MS-Access-the-Hard-Way)
*   [结论](#Conclusion)

## 查询来自 CFML 的访问文件

上周工作时，我需要从 Microsoft Access 数据库文件中迁移数据(唉！)到一个更“合意”的平台。我从高中起就没用过 Microsoft Access，因为我的开发机器上甚至没有安装 Microsoft Office，所以我需要一种从 CFML 连接到这个数据库的方法。

在谷歌上搜索了几下，我找到了 ucana Access T1，这是一款基于 T2 jack Access T3 的微软 Access JDBC 驱动程序。文档很容易理解，尽管我把自己与错误的 jar 类路径混淆了，很快我就有了一个到数据库文件的工作连接。

## JDBC 牛逼！

原来 JDBC 是相当可怕的！我认为我可以在一个微软程序的专有数据库文件上运行标准的 ANSI SQL(其中一些 Access 本身不支持)是很酷的，这个程序甚至没有安装在我的系统上。所有这一切都是**完全相同的开发者体验**，就像我在使用 MySQL 或 MSSQL 一样。(可能会降低性能。)

但是我跑题了。完成数据导入后，我决定将 UCanAccess jars 包装成一个 Lucee 扩展。为什么？

## 为什么是 Lucee 扩展？

非常简单:Lucee 扩展允许您在一个简单的即插即用过程中添加新的 CFML 特性、管理员 UI 或 java 库。不再下载`.zip`文件或将`.jar`文件复制到您的代码库中。对于 JDBC 驱动程序来说，Lucee 扩展是一个**特别是**简单的设置，因为我们所要做的就是向 Lucee 引擎提交一些 jar，并指示 Lucee 将它们共同识别为 JDBC 驱动程序。

## 创建 Lucee 扩展

我开始下载朱利安·哈利威尔的 [`lucee-mariadb`](https://github.com/cfsimplicity/lucee-mariadb) 扩展作为基础。(谢谢，朱利安！)经过一些调整，我的存储库看起来是这样的:

*   `extension/` -存放 Lucee 扩展源代码。
*   `box.json` -将扩展放在 Forgebox.io 上，并定义构建和发布扩展到 Forgebox 的脚本。
*   `buildExtension.cfc` -生成新的。拉上拉链。在`box.json`中每当扩展版本发生变化时。
*   正确的文档记录至关重要！

`extension/`文件夹的内容如下所示:

*   `context/`
    *   `admin/`
    *   `dbdriver/`
        *   增加了管理员用户在 Lucee Admin 中创建 UCanAccess 数据源的能力。
*   存放任何需要加载到 Lucee 中的 jar 文件。
*   `META-INF/`
    *   `logo.png` -在浏览 Lucee 管理中的扩展时显示该徽标。
    *   `MANIFEST.MF` -清单文件定义 Lucee 扩展配置。

我修改了`MANIFEST.MF`文件来指定扩展名和一些 JDBC 设置。我遵循了关于这个的[“Lucee 5 中的扩展”](https://docs.lucee.org/guides/lucee-5/extensions.html)文档，这在整个过程中帮助很大。

```
Manifest-Version: 1.0
Built-Date: 2019-07-11 09:00:00
version: "0.0.1"
id: "007CA05C-E789-4769-A8B8C266E5675F7A"
name: "UCanAccess"
description: "JDBC Driver for the MS Access database."
category: "Datasource"
lucee-core-version: "5.0.0.019"
start-bundles: false
jdbc: "[{'label':'UCanAccess','id':'ucanaccess','connectionString':'jdbc:ucanaccess:///{file}','class':'net.ucanaccess.jdbc.UcanaccessDriver'}]" 
```

接下来，我将 ucana access`.jar`文件及其四个依赖 jar 放在了`jar/`文件夹中。

接下来，我将`context/admin/dbdriver/MariaDB.cfc`文件重命名为`UCanAccess.cfc`,并修改它，用 UCanAccess 替换任何提到 MariaDB 的内容。我甚至不确定这个文件是做什么用的，但我认为它可能是将扩展配置为一个“适当的”JDBC 驱动程序，并添加了从 Lucee admin UI 创建 UCanAccess 数据源的能力。

***注意:**我还没有测试过这个数据源 UI。我有一种感觉，我需要更新我的`UCanAccess.cfc` dbdriver 文件，用[ucana access 连接属性](http://ucanaccess.sourceforge.net/site.html#examples)替换 MariaDB 连接属性。有道理，只是我还没到那一步。*

至此，延期“大功告成”。只需创建一个`extension`文件夹的 zip 文件并上传到 Lucee Admin 中，就可以安装扩展了。我们还可以创建一个`.zip`文件，将文件扩展名改为`.lex`，并将其放入 Lucee 上下文的`deploy/`文件夹中

## 用 CommandBox 和 ForgeBox 封装 Lucee 扩展

创建这个扩展的棘手部分是打包它。我创建了一个名为`buildExtension.cfc`的 CommandBox 任务，它创建了供 Lucee 使用的`.zip`和`.lex`文件，并将这些文件提交给存储库。

```
component {

    function run() {
        generateZips();
        commitUpdates()
    }

    function generateZips() {
        cfzip( action="zip", source="extension", file="lucee-ucanaccess.zip", overwrite="true" ) {}
        cfzip( action="zip", source="extension", file="lucee-ucanaccess.lex", overwrite="true" ) {}

        print.line( "Generated lucee-ucanaccess.zip and lucee-ucanaccess.lex" );
    }

    function commitUpdates(){
        var gitStatus = command( '!git' )
            .params( 'status' )
            .run( returnOutput=true );

        // git add will error (thus halting the release) if these files are unchanged.
        // So only proceed if `git status` says there are changed files. :)
        if ( gitStatus DOES NOT CONTAIN "lucee-ucanaccess" ) {
            print.redLine( "Nothing new to release" );
            return;
        }

        command( '!git' )
            .params( 'add lucee-ucanaccess.*' )
            .run();

        command( '!git' )
            .params( 'add lucee-ucanaccess.zip' )
            .run();

        command( '!git' )
            .params( 'commit -m "Add new release .lex and .zip"' )
            .run();

        print.greenLine( "New release with .lex and .zip" );
    }
} 
```

我通过从 CommandBox 运行`task run buildExtension`测试了任务的工作情况。一旦我让它工作了，我就把下面的`scripts`添加到我的`box.json`中:

```
"scripts":{  "postVersion":"package set location='https://bitbucket.org/michaelborn_me/ucanaccess/src/v`package version`/lucee-ucanaccess.zip'",  "onRelease":"task run buildExtension && publish",  "postPublish":"!git push --follow-tags"  } 
```

旁白:我从布拉德·伍德那里获得了这些包装脚本，因为他在《CFML 懈怠》中提到了它们。(*第 999 个理由:在闲暇时密切关注布拉德·伍德...*)它们也记录在 [CommandBox 包脚本](https://commandbox.ortusbooks.com/package-management/package-scripts)中。我在我写的每个包中都包含了这些脚本的一些变体，因为它们使得发布新的 Forgebox 版本变得超级容易和一致。

这些脚本通过使用 CommandBox 中的`package version`或`bump`命令来自动化 Forgebox 的发布过程。我可以在 CommandBox 中运行`bump --major`、`bump --minor`、`bump --patch`，甚至`package version 0.7.2`，CommandBox 会:

1.  创建并提交一个引用新版本号的 git 标签
2.  运行我的`postVersion`脚本来设置下载 URL
3.  运行我的`onRelease`脚本以
    1.  将`extension/`文件夹压缩成可安装的`.zip`和`.lex`文件，并且
    2.  发布新版本
4.  推送任何代码更改，包括我的新 git 标签版本号。

就这样，我运行了`bump --minor`，我的新 Lucee 扩展缩放到了 ForgeBox！

## 使用 UCanAccess Lucee 扩展

安装扩展的三个简单步骤:

1.  在指向`https://forgebox.io`的 Lucee 服务器管理中添加一个新的扩展提供者
2.  在“未安装”部分找到“UCanAccess”扩展名
3.  单击扩展，然后单击“安装”

在这篇博文的前面，我承认我还没有测试过从 Lucee Admin 创建数据源。在我开始工作之前，创建 UCanAccess 数据源最简单的方法是在您的`Application.cfc` :
中手动创建

```
msAccessDB = {
   class: "net.ucanaccess.jdbc.UcanaccessDriver",
   connectionString: "jdbc:ucanaccess://C:\Users\me\my\FILENAME.accdb"
}; 
```

一旦你这样做了，你应该能够使用数据源连接到一个 MS Access 数据库文件并运行查询:

```
var query = queryExecute( "SELECT COUNT(*) FROM pages", {}, { datasource: "msAccessDB" } ); 
```

就像这样，您正受益于 JDBC 和 Lucee 扩展的力量。(还有 UCanAccess。还有杰克塞斯。当然还有我自己！)

## 艰难地连接到 MS Access

如果你想忽略我所有的努力工作，并做这个**硬方式**(也许你是在 Adobe ColdFusion？)，您可以按照以下过程连接到 MS Access 数据库:

1.  下载并解压[ucana access。zip 文件](https://sourceforge.net/projects/ucanaccess/files/latest/download)
2.  导航到`UCanAccess-{version}.bin/`，将这五个`.jar`文件复制到您的项目`lib/`文件夹中:
    1.  `ucanaccess-{version}.jar`
    2.  `lib/commons-lang-{version}.jar`
    3.  `lib/commons-logging-{version}.jar`
    4.  `lib/hsqldb.jar`
    5.  `lib/jackcess-2.1.11.jar`
3.  在`Application.cfc`中，设置`this.javaSettings.loadPaths = [ "./lib/" ];`来指示 Lucee 或 ACF 加载 jar 文件
4.  在`Application.cfc` (Lucee)或 Adobe CF Administrator 中，添加一个具有以下两个属性的数据源:
    1.  将类路径引用为`net.ucanaccess.jdbc.UcanaccessDriver`
    2.  设置一个连接字符串，以`jdbc:ucanaccess://`开始，以 MS Access 数据库文件的完整路径结束。

现在要提醒的是，我不确定这在 Adobe CF 中是否有效。我使用 ACF Admin 创建数据源已经有几年了，它*可能不*支持自定义 JDBC 连接字符串和类路径——我不知道！请随意指导我——我很乐意更新这篇博客文章。

在 Lucee 中，你可以将一个 datasource 结构放到你的`Application.cfc`中，看起来像这样:

```
msAccessDB = {
   class: "net.ucanaccess.jdbc.UcanaccessDriver",
   connectionString: "jdbc:ucanaccess://C:\Users\me\my\FILENAME.accdb"
}; 
```

## 结论

*   Java 库很棒，能够在 CFML 使用它们也很棒。
*   大声喊出来。
*   Lucee 扩展实际上非常简单，至少对于打包 jar 来说是这样。

感谢阅读！