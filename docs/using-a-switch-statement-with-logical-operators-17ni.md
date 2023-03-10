# 将 Switch 语句与逻辑运算符一起使用

> 原文：<https://dev.to/jasterix/using-a-switch-statement-with-logical-operators-17ni>

JavaScript 的 switch 语句是一个非常强大的工具，但我通常避免使用它，因为它不像 if 语句或三元运算符那样可预测。但是在完成 Codesmith 的 CSX 挑战时，我决定使用 switch 来解决其中一个问题，并在这个过程中学到了一些有趣的东西。

#### 这是挑战:

```
Create a function gradeCalculator which takes a grade (number) and returns its 
letter grade.

grades 90 and above should return "A"
grades 80 to 89 should return "B"
grades 70 to 79 should return "C"
grades 60 to 69 should return "D"
59 and below should return "F" 
```

Enter fullscreen mode Exit fullscreen mode

#### 下面是我的初步解决方案:

```
function gradeCalculator(grade) {
  switch (grade) {
    case (grade >= 90):
      return "A"
    case grade >= 80:
      return "B"
    case grade >= 70:
      return "C"
    case grade >= 60:
      return "D"
    case grade <= 59:
      return "F"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

你能发现这个错误吗？一开始我不明白为什么终端会返回

```
undefined
undefined
undefined
undefined
undefined 
```

Enter fullscreen mode Exit fullscreen mode

但是快速的谷歌搜索把我带到了一个解决这个问题的 StackOverflow 讨论。

#### 答案

基本上，JavaScript 试图将括号中的表达式与案例的值进行比较。

如果`grade = 92`，`grade >= 90:`将返回`true`，但是我有我的 switch 语句来比较`true`和等级(或 92)。True === 92 返回未定义

#### 表述我的 switch 语句的正确方式是:

```
function gradeCalculator(grade) {
  switch (true) {
    case (grade >= 90):
      return "A"
    case grade >= 80:
      return "B"
    case grade >= 70:
      return "C"
    case grade >= 60:
      return "D"
    case grade <= 59:
      return "F"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

点击查看 StackOverflow 讨论[。](https://stackoverflow.com/questions/2312817/javascript-switch-with-logical-operators)