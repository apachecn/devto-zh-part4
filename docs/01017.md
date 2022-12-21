# C #中的列表格式

> 原文：<https://dev.to/ennor/list-formatting-in-c-4lgp>

今天我需要用 C 格式化一个单词列表，最后我写了自己的列表格式化程序，这很令人满意(我喜欢烦躁的字符串操作)。目标是这样的:给定一个单词列表，如{“苹果”、“香蕉”、“芒果”}，创建一个字符串，如“苹果、香蕉和芒果”。乍一看，这很简单:我们遍历列表，用逗号将每个单词附加到结果中，当我们附加最后一个单词时，用“and”作为前缀。

```
char *format_list(int argc, const char **argv, char *output, const char *and) {
  output[0] = '\0';
  for (int i = 0; i != argc; ++i) {
    strcat(output, argv[i]);
    strcat(output, ", ");
    if (argc == i + 2) {
      strcat(output, and);
      strcat(output, " ");
    }
  }
  return output;
}

void print_fruit(void) {
    const char * fruit[] = {"apple", "banana", "mango"};
    char buffer[1024];
    puts(format_list, 3, fruit, buffer, "and");
} 
```

Enter fullscreen mode Exit fullscreen mode

警告:这个函数不做边界检查，所以输出缓冲区需要有足够的空间来存放结果字符串。这是糟糕的函数设计，添加必要的检查并不难，但会使人更难理解这段代码真的很琐碎。

然而，我需要我的解决方案能在其他语言中工作。我们可以使用 gettext 来翻译输入和单词“and”，将单词翻译成依赖于地区的替代词，如下所示:

```
void print_fruit(void) {
    const char * fruit[3];
    char buffer[1024];
    fruit[0] = gettext("apple");
    fruit[1] = gettext("banana");
    fruit[2] = gettext("mango");
    puts(format_list, 3, fruit, buffer, gettext("and"));
} 
```

Enter fullscreen mode Exit fullscreen mode

这样(用正确的。po 文件和 libgettext)如果我们使用的是德国语言环境，我们会得到“Apfel，Banane，und Mango”。这有一个问题:德语不使用牛津逗号。而我不说的其他语言，根本不会用同样的规则写列表。有些把 and 放在开头(想想“苹果和香蕉，芒果”)。我们需要的不仅仅是“和”来封装所有这些规则，而不是为每种语言编写单独的函数。

抄袭来自 [icu4j 库](http://icu-project.org/apiref/icu4j/com/ibm/icu/text/ListFormatter.html)的灵感，这是我想到的函数:

[https://github . com/ennorehling/clibs/blob/master/format . c # L29](https://github.com/ennorehling/clibs/blob/master/format.c#L29)

我没有包括整个文件，因为有了边界检查，它变得相当长，但它确实有用，并且可以通过一组模式进行定制:

*   两个单词的列表(英语:“{0}和{1}”)
*   具有 3 个或更多元素的列表的开头(英语:“{0}、{1}”)
*   具有 3 个或更多元素的列表中间(英语:“{0}、{1}”)
*   包含 3 个或更多元素的列表的结尾(英语:“{0}”和{1}”)

如果不使用牛津逗号(我不会评判你)，那么最后一个字符串应该是“{0}和{1}”。

顺便说一下，这是我第一次在 C 语言中使用 goto，我向 Ed Dijkstra 和所有计算机科学老师道歉，他们反复告诉我，永远不要这样做。我犯了法，我不后悔！