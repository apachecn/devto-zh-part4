# 我尝试了 10 种编程语言，我喜欢它

> 原文：<https://dev.to/karataev/i-tried-10-programming-languages-and-i-liked-it-2kfn>

在过去的几年里，我一直在日常工作中使用 JavaScript。但是其他编程语言呢？大部分和 JS 一样成长进化。我认为看看 10 种不同编程语言的结构和语法会很有趣。

我决定用各种语言编写 FizzBuzz 程序。这是一个简单的算法，但需要知道如何编写循环、条件和声明变量。这应该是足够的介绍。

### 1。Java Script 语言

我先说最熟悉的一个。
REPL

```
for (let i = 1; i <= 100; i++) {
  let result = '';
  if (i % 3 === 0 && i % 5 === 0) result = 'FizzBuzz';
  else if (i % 3 === 0) result = 'Fizz';
  else if (i % 5 === 0) result = 'Buzz';
  else result = i;
  console.log(result);
} 
```

Enter fullscreen mode Exit fullscreen mode

简单明了。感觉轻而易举。

### 2。以打字打的文件

只需添加类型，您就可以开始了。
REPL

```
let result: string;
for (let i:number = 1; i <= 100; i++) {
  if (i % 3 === 0 && i % 5 === 0) result = 'FizzBuzz';
  else if (i % 3 === 0) result = 'Fizz';
  else if (i % 5 === 0) result = 'Buzz';
  else result = String(i);
  console.log(result);
} 
```

Enter fullscreen mode Exit fullscreen mode

### 3。计算机编程语言

过去我尝试过几次 Python，但是我可以说我一生中花在编程 Python 上的时间不超过 10 个小时。我只记得有必要用缩进来代替花括号😂第一个困难来了。我如何创建一个循环？逻辑运算符的语法是什么？If/elif。谷歌谷歌谷歌。

```
result = ''
for i in range(1, 101):
  if i % 3 == 0 and i % 5 == 0:
    result = 'FizzBuzz';
  elif i % 3 == 0:
    result = 'Fizz'
  elif i % 5 == 0:
    result = 'Buzz'
  else:
    result = i;
  print(result) 
```

Enter fullscreen mode Exit fullscreen mode

### 4。基础

怀旧的时间到了。QBasic 是我小时候的语言。我记得用线条和圆圈命令画复杂的图片🤓
20 年前，GOTO 指令到处都在使用，甚至被认为有害的 [Go To 语句](https://homepages.cwi.nl/~storm/teaching/reader/Dijkstra68.pdf)写于 1968 年。
写 FizzBuzz 的时候，我注意到字符串变量必须以美元符号结尾，否则编译器会对你大喊，哇。
显式地强制类型也是必要的。没有 JS 中的动态类型强制！
[REPL](https://repl.it/@karataev/QBasic-FizzBuzz)

```
result$ = ""
FOR i = 1 TO 100
IF i MOD 3 = 0 AND i MOD 5 = 0 THEN
  result$ = "FizzBuzz"
ELSE IF i MOD 3 = 0 THEN 
  result$ = "Fizz"
ELSE IF i MOD 5 = 0 THEN 
  result$ = "Buzz"
ELSE 
  result$ = STR$(i)
END IF
PRINT result$
NEXT i 
```

Enter fullscreen mode Exit fullscreen mode

### 5。C++

我记得 C++是一种面向“真正的程序员”的语言，有指针、手动内存分配和所有那些低级的东西。
再次从数字到字符串的显式强制。此外，包含有用函数的标准库也是必要的。

```
#include <iostream>
#include <string> 
int main() {
  std::string result = "";
  for (int i = 1; i <= 100; i++) {
    if (i % 3 == 0 && i % 5 == 0) result = "FizzBuzz";
    else if (i % 3 == 0) result = "Fizz";
    else if (i % 5 == 0) result = "Buzz";
    else result = std::to_string(i);
    std::cout << result << std::endl;
  }

} 
```

Enter fullscreen mode Exit fullscreen mode

### 6。Java 语言(一种计算机语言，尤用于创建网站)

血腥企业的语言。对我来说，Java 看起来像 TypeScript 是一个惊喜。学习 TypeScript 以一种语言的价格了解两种语言！😄
REPL

```
class Main {
  public static void main(String[] args) {
    String result = "";
    for (int i = 1; i <= 100; i++) {
      if (i % 3 == 0 && i % 5 == 0) result = "FizzBuzz";
      else if (i % 3 == 0) result = "Fizz";
      else if (i % 5 == 0) result = "Buzz";
      else result = String.valueOf(i);
      System.out.println(result);
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 7。锈

我对铁锈几乎一无所知。它就像 Mozilla 使用的 C++，可以方便地编写操作系统的核心。对于前端开发来说不太方便。
REPL

```
fn main() {
  let mut res = "";
  for i in 1..101 {
    if i % 3 == 0 && i % 5 == 0 {
      res = "FizzBuzz";
    } else if i % 3 == 0 {
      res = "Fizz";
    } else if i % 5 == 0 {
      res = "Buzz"
    } else {
      println!("{}", i);
      continue;
    }
    println!("{}", res);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 8。服务器端编程语言（Professional Hypertext Preprocessor 的缩写）

看起来很有趣，每个变量都必须以$开头。
REPL

```
<?php

for ($i = 1; $i <= 100; $i++) {
  if ($i % 3 == 0 && $i % 5 == 0) $res = "FizzBuzz";
  else if ($i % 3 == 0) $res = "Fizz";
  else if ($i % 5 == 0) $res = "Buzz";
  else $res = $i;
  echo "$res \n";
} 
```

Enter fullscreen mode Exit fullscreen mode

### 9。红宝石

与其他语言相比，Ruby 拥有完全不同的循环语法。
REPL

```
res = ""
1.upto 100 do |i|
  res = ""
  if i % 3 == 0 && i % 5 == 0
    res = "FizzBuzz"
  elsif i % 3 == 0
    res = "Fizz"
  elsif i % 5 == 0
    res = "Buzz"
  else
    res = i
  end

  puts res
end 
```

Enter fullscreen mode Exit fullscreen mode

### 10。(((Clojure)))

Clojure 是一种有趣的函数式语言，到处都是括号。函数式编程是另一个世界，需要采用不同的思维模式。谁会写`(+ 1 2)`这样的算术表达式，对吧？实际上 Clojure 中的 FizzBuzz 启发了我更深入地学习语言和生态系统。
T3】REPLT5】

```
(defn FizzBuzz [] (
  (loop [x 1]
  (when (<= x 100)
    (if (= 0 (rem x 15)) 
      (println "FizzBuzz")
      (if (= 0 (rem x 3)) 
        (println "Fizz")
        (if (= 0 (rem x 5))
          (println "Buzz")
          (println x)
        )
      )
    )
    (recur (+ x 1))))
))

(FizzBuzz) 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

仅此而已！尝试十种不同的编程语言是一种有趣但有时令人沮丧的经历。它们可能看起来不同，但我认为它们都有很多共同点——它们都是帮助将人类可以理解的思想转化为计算机可以理解的指令的工具。