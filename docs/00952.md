# Hello World:从编程开始

> 原文：<https://dev.to/immo/hello-world-starting-with-programming-kgn>

你好世界。编程的逻辑。人们学习一门新语言时做的第一件事。新的旅程开始了。但是学习一门新语言有一个大问题，那就是:“我从哪里开始？”

回答这个问题可能很难，尤其是当你不想花很多钱的时候。一个简单的`Hello World`程序不会让你走得太远，它只会教你最基本的东西。

考虑这个代码:

```
#include <iostream> int main() {
    std::cout << "Hello World" << std::endl;
    return 0;
} 
```

这是 C++里的`Hello World`。正如你可能看到的，这是一个非常基础的程序。你从中了解到的是`std::cout << "" << std::endl;`打印文本。然而，除了结束打印之外，你不会学到更多，甚至不知道`std::cout`真正做什么，也不知道`std::endl`做什么。

在这一点上，你可能会问自己:“那么，我怎样才能学到更多呢？东西是如何工作的？”和其他事情一样，你必须阅读或做练习来学习更多。最好的学习方法因人而异。对我来说，做互动教程对我的帮助最大，但涉及到 C++这样的语言，就没那么容易找到免费教程了。就我个人而言，我真的很喜欢学习 Codecademy，他们有 C++课程，但不太深入，很难在语言方面取得进展。

如果你更喜欢阅读而不是做互动教程，我会强烈推荐 [GoalKicker](https://goalkicker.com) ，里面有一堆非常好看的编程书籍。

我真正喜欢做的另一件事是“做就是了”的方法，在这种方法中，你可以用你知道的一点点东西来玩，用谷歌搜索你不知道的东西，直到你让程序工作为止。加上入门书籍，这是一个非常好的编程体验。

在对语言进行了一段时间的摸索之后，您将最终了解 Hello World 程序的内部内容:

```
#include <iostream> // imports the "iostream" header, which allows for input and output, hence the *io*i, literally means input/output stream 
int main() { // the main function, returns an integer, which is the exit code. 0 means success, everything else means error.
    std::cout << "Hello World" << std::endl; // cout prints using the "<<" operator, "Hello World" is the output text, which could also be a variable, and std::endl is an alias for '\n', the newline operator.
    return 0; // program succeeded
} 
```

学习编程可能相当具有挑战性，但像其他事情一样，你最终会抓住节奏并取得成功。我对编程初学者的建议是从简单开始，使用 Python 或 Node 之类的语言。JS 来学习编程的基础，然后继续学习更有挑战性的语言，比如 C++。

理解编程语言是编写好代码的关键。知道什么更好用，什么有用，什么有用，什么没用是编码的关键。当然，你不一定需要知道所有这些来编写功能性代码，但是如果你想让它工作得更好更稳定，你就需要知道这些。

开始学习编程是令人兴奋的，可能会感到害怕，但当你做得对的时候，真的会有回报。编程是一种体验，可以真正让人们了解很多技术是如何工作的。你可以对事物是如何工作的和事物过去是如何工作的感到好奇，你甚至可以对过去人们是如何编程的感到好奇，并了解一些著名的程序员，也许还有一些人创造了他们自己的编程语言并因此而出名。谁知道呢，也许有一天你也会成为其中的一员。

追随你的梦想，志存高远，有一天你可能会因你的作品而出名。