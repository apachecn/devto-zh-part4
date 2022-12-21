# 使用 DOI 标签作为 Pandoc 的引用

> 原文：<https://dev.to/aeroreyna/using-doi-tags-as-references-with-pandoc-id3>

Pandoc 是一个强大的文本转换工具，它允许在 Markdown 中完整地编写科学文档，并将其转换为适当格式的 pdf、web 文档、Latex 甚至 Docx 文件。通过使用过滤器，pandoc 能够将降价功能扩展到以前出版的作品中的参考文献，并利用图形、方程式、表格等。编号和内部引用。

我个人发现两个过滤器非常有用:Pandoc-citeproc 和 Pandoc-crossref。Citeproc 是一个过滤器，它在文本中查找@referencetag 格式的引用，并在文本和文档末尾的引用块中用指定的样式(如 APA 或 IEEE)对它们进行格式化。
另一方面，crossref 过滤器为我们提供了一种插入公式、图形、表格和列表(代码块)的适当方式，通过这种方式，它们可以自动、正确地编号，并通过自定义标签引用。

Pandoc-citeproc 需要指向一个 biblatex <sup id="fnref1">[1](#fn1)</sup> 文件，其中包含所引用作品的信息，还可以选择一个 csl 文件来确定所使用的引用样式。这两者都可以在 Markdown 文件中使用文档开头的 YAML 块直接指示，如下所示:

```
paper.md

--------
title: Pandoc doi2bib filter
bibliography: library.bib
csl: csl/apa.csl
--------

The author of [@Fausto2019] has mentioned this issue before.

# Refereces:

~~~

and the .bib file should contain the information of the reference such as:

~~~biblatex
@article{@Fausto2019,
    doi = {10.1007/s10462-018-09676-2},
    url = {https://doi.org/10.1007%2Fs10462-018-09676-2},
    year = 2019,
    month = {jan},
    publisher = {Springer Nature},
    author = {Fernando Fausto and Adolfo Reyna-Orta and Erik Cuevas and {\'{A}}ngel G. Andrade and Marco Perez-Cisneros},
    title = {From ants to whales: metaheuristics for all tastes},
    journal = {Artificial Intelligence Review}
}
~~~

Then the command `pandoc -s paper.md --filter pandoc-citeproc -t html` return the converted text from markdown to html with the references included:

~~~html
<!--Partial Result:-->
<body>
<header id="title-block-header">
<h1 class="title">Pandoc doi2bib filter</h1>
<p class="date">2019-07-01 10:35:49</p>
</header>
<p>The author of <span class="citation" data-cites="Fausto2019">(Fausto et al. 2019)</span> has mentioned this issue before.</p>
<h1 id="refereces" class="unnumbered">Refereces:</h1>
<div id="refs" class="references" role="doc-bibliography">
<div id="ref-Fausto2019">
<p>Fausto, Fernando, Adolfo Reyna-Orta, Erik Cuevas, ├üngel G. Andrade, and Marco Perez-Cisneros. 2019\. ÔÇ£From Ants to Whales: Metaheuristics for All Tastes.ÔÇØ <em>Artificial Intelligence Review</em>, January. Springer Nature. <a href="https://doi.org/10.1007/s10462-018-09676-2">https://doi.org/10.1007/s10462-018-09676-2</a>.</p>
</div>
</div>
</body>
~~~

In this way, it is fairly simple to write and manage the document and presented in the required format for collages to collaborate or to be submitted for publication (most likely in Latex).

However, with this framework the creation and maintaining of the references file (.bib) and the referring tags of the cited works is left to be done manually or by third parties, such as reference managers like Zotero or Mendeley.

Due to the fact that most recent publications make use of the digital object identifier (DOI)[^2], it is possible to use this index as the citation tag in our documents.
By doing so, it is warranted that all citations reference to a unique document, different to usual tags on which an author could potentially have several publications for each year.
This also open the window for further automatization, as there is reliable web services that offers the citation information of any given DOI, such as [https://dx.doi.org/]().

This concept give birth to a new pandoc filter called [doi2bib](https://github.com/aeroreyna/pandoc-doi2bib).
This filter make use of specified bibliography file (only .bib) in the YAML configuration, it search for all references with the format @DOI:XXX.XXX\XXXXXXX and updates the this file accordingly.
This means that any new reference is automatically added using the reliable information offered in the correct format by _doi.org_.

This tool offers the following benefits:

 - The specified file can be an empty file, previously existed .bib filed or not existent.

 - Only newly references required to be downloaded, therefore it does not add significantly time of compilation.

 - Several document can share this .bib file, or use a global one for all your documents.

 - If all your reference uses this format, a new file with only the current citations in order of citation can be generated simply by changing the specified bibliography file in the document.

To make use of this filter, just download the last build from the [Github](https://github.com/aeroreyna/pandoc-doi2bib) and paste it in the same Path of your pandoc executable.
Then this can be implemented using the command `pandoc -s paper.md --filter pandoc-doi2bib --filter pandoc-citeproc -o paper.pdf`

~~~markdonw
paper.md

--------
title: Pandoc doi2bib filter
bibliography: library.bib
csl: csl/apa.csl
--------

The author of [@DOI:10.1007/s10462-018-09676-2] has mentioned this issue before.

# Refereces:

~~~

which results in:

![](https://thepracticaldev.s3.amazonaws.com/i/otd6iy7ftb5dkr0sjdaj.png)

I´m using this framework for my thesis and prospect publications, so I hope it might helps others as well. 
```

Enter fullscreen mode Exit fullscreen mode

* * *

1.  也可以使用其他文件类型，如 bibtex、json 或 yaml。 [↩](#fnref1)