# 现代 C++小片段:常量表达式

> 原文：<https://dev.to/jiangh/modern-c-in-small-pieces-constant-expressions-4loo>

现代 C++引入了`constexpr`说明符，允许声明
编译时常量表达式。这允许程序员
明确指定在编译时应该计算什么，而不是
猜测什么可能被编译器优化为常量。一个
更清晰的编译时间和运行时间的界限也使得其他的
编译时间工具更容易使用。

```
const auto a = 10;
constexpr auto b = a + 1;  // const b = 11;
constexpr auto c = a + b;  // const c = 21; 
```

所有用`constexpr`声明的变量都是隐式的`const`和
必须用在编译时可计算的表达式初始化，但是
反之则不成立。一个`const`常量可以用一个
运行时表达式初始化。

```
const auto a = std::rand();  // Ok.
constexpr auto b = a -1;  // compile error! 
```

一个函数的返回类型也可以是`constexpr`，只要它的
体没有运行时依赖，并且只应用于
`constexpr`参数。该函数在编译时执行，
，不为函数体生成代码。

```
constexpr auto smaller(auto a, auto b) {
  return a <= b ? a : b;
}

int main() {
  const auto MAX_GROUP_SIZE = 400;
  const auto MAX_USERS = 500;
  constexpr auto LIMIT = smaller(MAX_USERS, MAX_GROUP_SIZE);
  return 0;
} 
```

常量表达式也可以在`if`和
语句中用作条件。因为条件是在编译
时评估的，所以错误分支根本不会被编译。

```
if constexpr (sizeof(void*)*CHAR_BIT == 64) {
  std::cout << "Compiled for 64-bit OS." << std::endl;
} else {
  std::cout << "Not compiled for 64-bit OS." << std::endl;
} 
```

一个常量表达式可以由其他常量表达式组成，所以
你有可能在编译
时做一些非常复杂的计算。例如，下面的程序让编译器计算第 10 个斐波纳契数
:

```
constexpr unsigned int static_fib(unsigned int n) {
  if (n <= 1)
    return n;
  else
    return static_fib(n-1) + static_fib(n-2);
}

int main() {
  std::cout << static_fib(10) << std::endl;
  return 0;
} 
```

注意常量表达式中的函数必须是纯粹的
函数——不能有局部变量，因此不能有循环。编译器为上述程序生成的代码
相当于:

```
int main() {
  std::cout << 55 << std::endl;
  return 0;
} 
```

在传统的 C++中，要达到同样的效果，需要难看地滥用模板。