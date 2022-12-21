# 将参数解析成 D 中的结构

> 原文：<https://dev.to/jessekphillips/argument-parsing-into-structure-4p4n>

更新:把这个做成一个项目-

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[JesseKPhillips](https://github.com/JesseKPhillips)/[struct opt](https://github.com/JesseKPhillips/structopt)

### 从结构生成 GetOpt 方法调用

<article class="markdown-body entry-content container-lg" itemprop="text">

这个包来自我为自己写的一个小工具，然后在这里写博客。

[https://dev . to/jessekphillips/argument-parsing-into-structure-4p4n](https://dev.to/jessekphillips/argument-parsing-into-structure-4p4n)

它允许使用结构来定义所需的命令行参数。灵感来源于 [darg](http://code.dlang.org/packages/darg) 。

```
import structopt;

// Specify The Parameter Structure
struct Options
{
    @Option("threads", "t")
    @Help("Number of threads to use.")
    size_t threads;

    @Option("file")
    @Help("Input files")
    string[] files;
}

void main(string[] args) {
    Options props;

    // Pass in the struct to generate UDA for
    auto helpInfo = mixin(GenerateGetopt!props);

        defaultGetoptPrinter("Options: ",
          helpInfo.options);
        // Output to console:

        //Options:
        //-t --threads Number of threads to use.
        //      --file Input files
        //-h    --help This help information./
} 
```

</article>

[View on GitHub](https://github.com/JesseKPhillips/structopt)

这里有一个有趣的，从一些背景开始。Dlang 有一个标准的参数解析器， [getopt](https://dlang.org/phobos/std_getopt.html#.getopt) ，它提供了一个函数来指定解析器输出的参数。这个函数可以被多次调用，这有助于用它们自己的参数构建子命令。我真的很喜欢文档示例中展示的这个 API 的设计。

然而，在有些情况下，一个结构对于推动什么样的论点被接受是有意义的，因为首字母缩略词很重要，所以我想保持简洁。现在不像是没有库可以解析成像 [darg](http://code.dlang.org/packages/darg) 这样的结构，我不知道我的搜索，但仍然认为标准方法使事情更加一致。

d 有一些优秀的元编程和生成能力。与 C#或 Java 注释中的传统属性不同，D 没有运行时反射。当使用标准的 getopt 函数时，我想让这个结构构建我的选项和帮助消息，[这就是发生的事情](https://code.dlang.org/packages/struct%20opt)。

```
import std.traits;
import std.getopt;

// Specify The Parameter Structure
struct Options
{
    @Option("threads", "t")
    @Help("Number of threads to use.")
    size_t threads;

    @Option("file")
    @Help("Input files")
    string[] files;
}

void main(string[] args) {
    Options prop;

    // D's mixin requires a complete
    // statement to be built
    string GenerateGetopt(alias Options)() pure {
        import std.meta;
        import std.typecons;
        import std.format;
        auto ans = `auto helpInfo = getopt(args, `;
        static foreach(opt; FieldNameTuple!Options) {
            // getUDAs will obtain the User Defined Attribute
            // of the specified type
            ans ~= format("getUDAs!(prop.%s, Option)[0].cononical(),"
              ~" getUDAs!(prop.%s, Help)[0].msg, &prop.%s,", opt, opt, opt);
        }

        return ans ~ ");";
    }

    // Pass in the struct to generate UDA for
    mixin(GenerateGetopt!Options);

    if (helpInfo.helpWanted)
    {
        defaultGetoptPrinter("Options: ",
          helpInfo.options);
        // Output to console:

        //Options:
        //-t --threads Number of threads to use.
        //      --file Input files
        //-h    --help This help information./
    }
}

// -- These define the data type for the UDA

struct Help {
    string msg;
}

struct Option {
    string[] names;

    this(string[] names...) {
        this.names = names;
    }

    string cononical() {
        import std.algorithm;
        import std.conv;
        return names.joiner("|").to!string;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我也做类似的事情来填充基于环境变量的结构。