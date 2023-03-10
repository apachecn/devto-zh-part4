# 查找序列中的所有子序列

> 原文：<https://dev.to/ihhha/find-all-subsequences-in-the-sequence-329o>

让我们从函数
的签名开始

```
def groupSubseq[T](in: Seq[T])(p: (T, T) => Boolean): Seq[Seq[T]] 
```

这里的 **p** 是我们在将项目添加到现有的 subseq 或创建新的 subseq 之前使用的谓词

我们将使用 foldLeft

```
def groupSubseq[T](in: Seq[T])(p: (T, T) => Boolean): Seq[Seq[T]] = in
  .foldLeft(Seq.empty[Seq[T]])((seq, item) => seq match {
    case Nil => Seq(Seq(item)) // processing first element
    case currentSeq +: tail =>
      if (p(currentSeq.head, item)) {
        // new element satisfies the condition, add it to the subsequence
        (item +: currentSeq) +: tail 
      } else {
        // new element not satisfies the condition, start new subsequence
        Seq(item) +: seq
      }
  }) 
```

看来我们到此为止了。但让我解释一下))...这篇文章是我最近面临的几个类似任务的结果。

**任务 1**

*找出给定字符串中同一字符的所有序列，并统计每个序列中该字符的个数*

```
def countChar(seq: String): Seq[Char, Int] = ???

val test1 = "aaabbaaaabbb"

countChar(test1)

//res0: List((a,3), (b,2), (a,4), (b,3)) 
```

**任务二**

*统计给定 Int*
序列中相同符号的所有序列的个数

```
def countSign(seq: Set[Int]): Int = ???

val test1 = Seq(1, 2, 3, -3, -2, 6, 7, 8, 9, -3, -1, -6, 7)

countSign(test1)

//res0: 3 
```

利用我们最近的发明，我们可以很容易地解决这个问题

```
def countChar(seq: String) =
  groupSubseq(seq)(_ == _).map(seq => seq.head -> seq.length).reverse

countChar("aaabbaaacddddd")

// res0: Seq[(Char, Int)] = List((a,3), (b,2), (a,3), (c,1), (d,5))

def countSign(seq: Seq[Int]) = groupSubseq(seq)(_ * _ > 0).count(_.length > 2)

countSign(Seq(1, 2, 3, 4, -1, 1, -1, -2, -3, -4, 0, 0, 0))

// res1: Int = 2 
```

希望能在评论里看到大家的赞成和反对意见。感谢阅读。