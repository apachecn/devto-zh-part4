# 写一个 java 程序来按字典顺序比较两个字符串。

> 原文：<https://dev.to/pykashchain/write-a-java-program-to-compare-two-strings-lexicographically-3gie>

写一个 java 程序来按字典顺序比较两个字符串。

使用 compareTo 方法比较字符串。
compareTo 方法是根据字符的 unicode 值比较字符串。
它返回正负值和零值。
如果字符串相同，它返回零值，否则它的正值或负值取决于字符的 ASCII 值。
例如
第一串是 s1 =中间
第二串是 s2 =中间
所以它返回值为-1。
bcoz m 的值是 77，n 的值是 78 (ASCII)
所以 77 -78 = -1
所以返回值是-1。

[https://repl.it/@VikasTomar/Write-a-java-program-to-compare-two-strings?lite=true](https://repl.it/@VikasTomar/Write-a-java-program-to-compare-two-strings?lite=true)