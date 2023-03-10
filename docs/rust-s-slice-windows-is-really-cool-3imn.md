# 拉斯特的《slice::windows》真的很酷

> 原文：<https://dev.to/tindleaj/rust-s-slice-windows-is-really-cool-3imn>

*戈登·威廉姆斯在 Unsplash 上拍摄的封面照片*

> 这篇文章是概述我作为一个主要使用 JavaScript 的人学习 Rust 的观点的几篇文章之一。其余的[你可以在这里](https://medium.com/@tindleaj)找到。

Rust 有很多与它的原语和标准库相关的强大特性。我刚刚遇到的一个很酷的方法是 [`slice::windows`](https://doc.rust-lang.org/std/primitive.slice.html#method.windows) 。这个方法返回一个迭代器。可以指定迭代器长度。它让你在一个切片上迭代，并且在每一次迭代中都有一个特定大小的*窗口*。例如:

```
// windows.rs

let slice = ['w', 'i', 'n', 'd', 'o', 'w', 's'];

for window in slice.windows(2) {
  &println!{"[{}, {}]", window[0], window[1]};
}

// prints: [w, i] -> [i, n] -> [n, d] -> [d, o] -> [o, w] -> [w, s] 
```

这很酷，在 JavaScript 中没有类似的东西。我能想到的最接近的方法是:

```
// windows.js

let slice = ["w", "i", "n", "d", "o", "w", "s"];

slice.forEach((char, index) => {
  if (index === slice.length - 1) return;

  console.log(`[${slice[index]}, ${slice[index + 1]}]`);
}); 
```

JavaScript 方法肯定不太符合人体工程学，我真的很喜欢窗口的概念，它是迭代器的一种抽象。

感谢阅读。