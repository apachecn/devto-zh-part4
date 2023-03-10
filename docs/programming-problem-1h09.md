# 编程问题

> 原文：<https://dev.to/kevinmungai/programming-problem-1h09>

写一个程序，给定一些文件，可以找到所有的文件中含有字母" a "。

> 使用将自动关闭任何 I/O 的`with-open`宏。`clojure.string/includes?`函数将检查输入的每一行是否包含字母“a ”,并返回真或假。

```
(defn  contains-a?  [document]  (with-open  [rdr  (clojure.java.io/reader  document)]  (clojure.string/includes?  (line-seq  rdr)  "a")))  (filter  #(contains-a?  %)  [documents]) 
```

Enter fullscreen mode Exit fullscreen mode

下一步是:

1.  **尽可能快地尝试并成功**，也就是说，一旦程序检测到一个“a ”,它应该返回 true 并停止浏览该文件，

2.  **利用 Java 的`AsynchronousFileChannel`** 和`core.async`来并行化工作？不知道这将如何工作。

灵感
[要旨](https://gist.github.com/mfikes/03172ce8dd62439431026b7b94ec201f)