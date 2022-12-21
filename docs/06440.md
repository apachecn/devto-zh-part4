# R 中的多语种 FizzBuzz(加上:“为什么 Johnny 不会编码？”)

> 原文：<https://dev.to/hrbrmstr/polyglot-fizzbuzz-in-r-plus-why-can-t-johnny-code-4k9n>

我在[上看到了这篇文章，从黑客新闻 RSS 源上看到了数量惊人的不会编程的程序员。这篇文章链接到了另一篇关于同一主题的经典文章，你应该在继续之前阅读这两篇文章。](https://dev.to/mooreds/the-surprising-number-of-programmers-who-can-t-program-1jm6-temp-slug-640850)

回来了？太好了！让我们开始吃吧。

### hrbrmstr 为什么会关心这个？

后代 3 号去年在拉恩奥罗诺大学完成了他的第一年，但他想在夏天保持学术上的活跃(他主修天体物理学，知道他需要一些编程技能才能在他的领域中脱颖而出)，并参加了拉恩大学的虚拟 C++入门课程，每周一次讲座(14 周 IIRC)和每周一次作业，没有其他评分。

在看了所谓的标准 C++入门课程(如果一个人想成为计算机科学家，拉恩并不完全是首选院校)后，我对“约翰尼不会编码”并不感到惊讶。13 周的课程终于开始涵盖面向对象的概念，课程结束时对多态性的介绍很少。在此之前，大多数的赋值只是彼此之间的变化(从标准输入中读取，条件循环，打印输出)，没有程序超过 100 LoC(包括注释和空格)。这也不是“非计算机科学专业的计算机科学”课程。任何主修需要编程的研究领域的人都可以选修这门课程来满足其中一项要求，他们将永远走上使用 StackOverflow copypasta 来完成未来工作的道路。

我相当肯定#3 的大部分同学没有谷歌就不会编写 fizzbuzz，更肯定的是，大部分同学都不知道他们在这门课的大部分时间里并没有真正“用 C++编程”。

如果这就是大多数其他中等院校教授计算机编程基础知识的方式，难怪雇主很难找到合格的人才。

### 在这篇文章中，你有一个“R”标签——实际上是一些语言标签，那么代码在哪里呢？

在这篇文章引发了前一部分的哀叹之后，一个疯狂的、[@ coolbutused](http://twitter.com/coolbutuseless)式的想法出现在我的脑海中:“我想知道在 R 内部可以创建多少种不同语言的 FizzBuz 解决方案？”。

这个概念的标准是/曾经是，需要有一些`Rcpp::cppFunction()`、`reticulate::py_run_string()`、V8 上下文`eval()`类型的方法来让代码在-R 中运行，然后通过那些远超于任何其他语言的多语言可扩展性构造来运行。

在迷失在杂草中之前，还有一些关于语言包容性的其他想法:

*   Java 应不应该包含在内？我❤️ {rJava}，但是`cat()`取出 Java 代码并首先运行`system()`来编译它看起来像是作弊(尽管这有点像`cppFunction()`所做的)。如果您认为应该添加一个 Java 示例(或者在注释中添加该 Java 示例，或者在注释中添加它的链接!),请在注释中添加注释。).
*   我认为 Julia 应该在这个示例列表中，但我并不太关心它，所以没有加载 [{JuliaCall}](https://github.com/Non-Contradiction/JuliaCall) 并制作一个示例(同样，如果你能快速制作，请链接或发布一个)。
*   我认为 Lua 可以在这个例子中给出 [{luar}](https://github.com/noamross/luar) 的存在。如果你同意，试一试吧！
*   Go & Rust 编译的代码一旦被转换成 C 兼容的库，也可以在 R 中被调用(多亏了罗曼和耶鲁安)。这个多语言的例子应该也能说明这一点吗？
*   我还遗漏了哪些语言？

### 前述“野草”

每种语言 fizzbuzz 示例的一个标准是它们需要可读，而不是非常酷。这并不意味着解决方案不能有点创意。我们将仔细检查我编写的每一个代码。首先我们需要一些帮手

```
suppressPackageStartupMessages({
  library(purrr)
  library(dplyr)
  library(reticulate)
  library(V8)
  library(Rcpp)
}) 
```

R、JavaScript 和 Python 实现都在下面的`microbenchmark()`调用中。这里是 C 和 C++版本。C 实现很无聊也很简单，但是我们使用了`Rprintf()`,这样我们就可以捕获输出，而不会有任何输出缓冲问题影响计时。

```
cppFunction('
void cbuzz() {

  // super fast plain C

  for (unsigned int i=1; i<=100; i++) {
    if (i % 15 == 0) Rprintf("FizzBuzz\\n");
    else if (i % 3 == 0) Rprintf("Fizz\\n");
    else if (i % 5 == 0) Rprintf("Buzz\\n");
    else Rprintf("%d\\n", i);
  }

}
') 
```

即使在 C++环境中,`cbuzz()`示例也很好，但是我们可以利用一些 C++11 矢量化特性，在 C++环境中保持正式状态，并使用一些有趣的特性，如 lambdas。这将比 C 版本慢一点，并且消耗更多的内存，但是展示了一些人们可能不熟悉的特性:

```
cppFunction('
void cppbuzz() {

  std::vector<int> numbers(100); // will eventually be 1:100
  std::iota(numbers.begin(), numbers.end(), 1); // kinda sorta equiva of our R 1:100 but not exactly true

  std::vector<std::string> fb(100); // fizzbuzz strings holder

  // transform said 1..100 into fizbuzz strings
  std::transform(
    numbers.begin(), numbers.end(), 
    fb.begin(),
     -> std::string { // lambda expression are cool like a fez
        if (i % 15 == 0) return("FizzBuzz");
        else if (i % 3 == 0) return("Fizz");
        else if (i % 5 == 0) return("Buzz");
        else return(std::to_string(i));
    }
  );

  // round it out with use of for_each and another lambda
  // this turns out to be slightly faster than range-based for-loop
  // collection iteration syntax.
  std::for_each(
    fb.begin(), fb.end(), 
     { Rcout << s << std::endl; }
  );

}
', 
plugins = c('cpp11')) 
```

这两个功能现在都可以用于 r。

接下来，我们需要准备运行 JavaScript 和 Python 代码，所以我们将初始化这两个环境:

```
ctx <- v8()

py_config() # not 100% necessary but I keep my needed {reticulate} options in env vars for reproducibility 
```

然后，我们告诉 R 捕获所有的输出。在这个用例中使用`sink()`比`capture.output()`好一点，因为为了避免嵌套调用，我们需要用与`py_capture_output()`相同的方式来处理 Python stdout，以便在我们的测量中保持公平:

```
output_tools <- import("rpytools.output")
restore_stdout <- output_tools$start_stdout_capture()

cap <- rawConnection(raw(0), "r+")
sink(cap) 
```

下面是 tidy 和 base R 多元宇宙中的一些实现。有的用矢量化；有些没有。这将让我们比较解决方案的总体“速度”。如果你对 R 中的*可读*解决方案有其他建议，请在评论中留言:

```
microbenchmark::microbenchmark(

  # tidy_vectors_case() is slowest but you get all sorts of type safety 
  # for free along with very readable idioms.

  tidy_vectors_case = map_chr(1:100, ~{ 
    case_when(
      (.x %% 15 == 0) ~ "FizzBuzz",
      (.x %% 3 == 0) ~ "Fizz",
      (.x %% 5 == 0) ~ "Buzz",
      TRUE ~ as.character(.x)
    )
  }) %>% 
    cat(sep="\n"),

  # tidy_vectors_if() has old-school if/else syntax but still
  # forces us to ensure type safety which is cool.

  tidy_vectors_if = map_chr(1:100, ~{ 
    if (.x %% 15 == 0) return("FizzBuzz")
    if (.x %% 3 == 0) return("Fizz")
    if (.x %% 5 == 0) return("Buzz")
    return(as.character(.x))
  }) %>% 
    cat(sep="\n"),

  # walk() just replaces `for` but stays in vector-land which is cool

  tidy_walk = walk(1:100, ~{
    if (.x %% 15 == 0) cat("FizzBuzz\n")
    if (.x %% 3 == 0) cat("Fizz\n")
    if (.x %% 5 == 0) cat("Buzz\n")
    cat(.x, "\n", sep="")
  }),

  # vapply() gets us some similiar type assurance, albeit with arcane syntax

  base_proper = vapply(1:100, function(.x) {
    if (.x %% 15 == 0) return("FizzBuzz")
    if (.x %% 3 == 0) return("Fizz")
    if (.x %% 5 == 0) return("Buzz")
    return(as.character(.x))
  }, character(1), USE.NAMES = FALSE) %>% 
    cat(sep="\n"),

  # sapply() is def lazy but this can outperform vapply() in some
  # circumstances (like this one) and is a bit less arcane.

  base_lazy = sapply(1:100, function(.x) {
    if (.x %% 15 == 0) return("FizzBuzz")
    if (.x %% 3 == 0) return("Fizz")
    if (.x %% 5 == 0) return("Buzz")
    return(.x)
  }, USE.NAMES = FALSE) %>% 
    cat(sep="\n"),

  # for loops...ugh. might as well just use C

  base_for = for(.x in 1:100) {
    if (.x %% 15 == 0) cat("FizzBuzz\n")
    else if (.x %% 3 == 0) cat("Fizz\n")
    else if (.x %% 5 == 0) cat("Buzz\n")
    else cat(.x, "\n", sep="")
  },

  # ok, we'll just use C!

  c_buzz = cbuzz(),

  # we can go back to vector-land in C++

  cpp_buzz = cppbuzz(),

  # some <3 for javascript

  js_readable = ctx$eval('
for (var i=1; i <101; i++){
  if (i % 15 == 0) console.log("FizzBuzz")
  else if (i % 3 == 0) console.log("Fizz")
  else if (i % 5 == 0) console.log("Buzz")
  else console.log(i)
}
'),

  # icky readable, non-vectorized python

  python = reticulate::py_run_string('
for x in range(1, 101):
  if (x % 15 == 0):
    print("Fizz Buzz")
  elif (x % 5 == 0):
    print("Buzz")
  elif (x % 3 == 0):
    print("Fizz")
  else:
    print(x)
')

) -> res 
```

关闭输出捕捉:

```
sink()
if (!is.null(restore_stdout)) invisible(output_tools$end_stdout_capture(restore_stdout)) 
```

我们使用了`microbenchmark()`，所以这里是结果:

```
res
## Unit: microseconds
## expr min lq mean median uq max neval cld
## tidy_vectors_case 20290.749 21266.3680 22717.80292 22231.5960 23044.5690 33005.960 100 e
## tidy_vectors_if 457.426 493.6270 540.68182 518.8785 577.1195 797.869 100 b   
## tidy_walk 970.455 1026.2725 1150.77797 1065.4805 1109.9705 8392.916 100 c  
## base_proper 357.385 375.3910 554.13973 406.8050 450.7490 13907.581 100 b   
## base_lazy 365.553 395.5790 422.93719 418.1790 444.8225 587.718 100 ab   
## base_for 521.674 545.9155 576.79214 559.0185 584.5250 968.814 100 b   
## c_buzz 13.538 16.3335 18.18795 17.6010 19.4340 33.134 100 a    
## cpp_buzz 39.405 45.1505 63.29352 49.1280 52.9605 1265.359 100 a    
## js_readable 107.015 123.7015 162.32442 174.7860 187.1215 270.012 100 ab   
## python 1581.661 1743.4490 2072.04777 1884.1585 1985.8100 12092.325 100 d 
```

说结果是 [![🤷🏻‍♀️](img/c893ca48c63a24d3b56c18273258ac65.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rf_ZQSRL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s.w.oimg/core/emoji/12.0.0-1/72x72/1f937-1f3fb-200d-2640-fe0f.png) 因为这是一个玩具例子，但我想表明耶鲁安的{V8}可以超级快，特别是当没有值编组要做，有些事情你可能认为应该更快，不是。

### 鳍

一定要添加修改或添加的链接或代码(尤其是前面提到的其他语言)。希望我对拉恩大学计算机科学项目的哀叹并不普遍适用于那里的所有编程课程。