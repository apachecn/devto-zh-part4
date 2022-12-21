# dev.to 上的 bug:Twitter share 上的&符号没有逃脱

> 原文：<https://dev.to/vepo/bug-on-dev-to-ampersand-not-escaped-on-twitter-share-1324>

打开[这篇文章](https://dev.to/emmawedekind/101-tips-for-being-a-great-programmer-human-36nl)，试着用 Twitter 分享它。
它会打开一个标签，上面有@ EmmaWedekind # dev community[https://dev . to/EmmaWedekind/101-Tips-For-be-A-Great-Programmer-Human-36nl](https://dev.to/emmawedekind/101-tips-for-being-a-great-programmer-human-36nl)的推文**“成为伟大程序员的 101 个技巧(**而不是**“成为伟大程序员的 101 个技巧(&人类))。**

为什么？因为&符号没有转义:

```
https://twitter.com/intent/tweet?text=%22101%20Tips%20For%20Being%20A%20Great%20Programmer%20(&%20Human)%22%20by%20@EmmaWedekind%20%23DEVcommunity%20https://dev.to/emmawedekind/101-tips-for-being-a-great-programmer-human-36nl 
```

我只是把 **&** 换成了 **%26** 然后工作起来:

```
https://twitter.com/intent/tweet?text=%22101%20Tips%20For%20Being%20A%20Great%20Programmer%20(%26%20Human)%22%20by%20@EmmaWedekind%20%23DEVcommunity%20https://dev.to/emmawedekind/101-tips-for-being-a-great-programmer-human-36nl 
```