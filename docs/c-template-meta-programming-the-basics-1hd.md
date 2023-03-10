# C++模板元编程-基础

> 原文：<https://dev.to/malwarebo/c-template-meta-programming-the-basics-1hd>

模板元编程(Template Meta-programming)是一种泛型编程技术，它使用极早期绑定来生成临时源代码，由编译器将其与其余源代码合并，然后进行编译。
为了彻底理解我们为什么需要模板编程，让我们回到[函数重载](https://en.wikipedia.org/wiki/Function_overloading)。

函数重载有些乏味，因为我们必须为每个函数重复相同的代码，但使用不同的变量和参数类型。但是，有一种方法可以避免这种情况。有可能创建一个配方，使编译器能够自动生成具有各种参数类型的函数。定义生成一组特定函数的方法的代码称为函数模板。

函数模板有一个或多个类型参数，我们通过为模板的每个参数传递一个具体的类型参数来生成一个特定的函数。因此，由函数模板生成的函数都有相同的基本代码，但是由我们传递的类型参数定制。

我们将编写一个使用函数重载和函数模板的例子，从一个数组中找到最大的元素。

```
int max(int max[], const int& len){
    int maximum(x[0]);
    for(int i = 0; i < len; ++i){
       if(maximum < x[i]) maximum = x[i];
    }
    return maximum;
}  

long max(long max[], const int& len){
    long maximum(x[0]);
    for(int i = 0; i < len; ++i){
       if(maximum < x[i]) maximum = x[i];
    }
    return maximum;
} 

double max(int max[], const int& len){
    double maximum(x[0]);
    for(int i = 0; i < len; ++i){
       if(maximum < x[i]) maximum = x[i];
    }
    return maximum;
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的例子展示了使用函数重载的一个函数的三个版本。我们看到，我们必须为函数的每个版本重复相同的代码。

现在让我们为同一个函数 **max()** 定义一个函数模板。

```
template<class T> T max(T x[], const int& len){
    T maximum(x[0]);
    for(int i = 0; i < len; ++i){
       if(maximum < x[i]) maximum = x[i];
    }
    return maximum;   
} 
```

Enter fullscreen mode Exit fullscreen mode

template 关键字将其标识为模板定义。template 关键字后面的尖括号括起了类型参数，这些参数用于创建函数的特定实例，用逗号分隔。在这个实例中，我们有一个类型参数， **T** 。在 **T** 之前的 class 关键字表示 **T** 是该模板的类型参数，class 是该类型的通称。因此，在 C++中我们有基本类型，比如 int 类型和 char 类型，我们也有自己定义的类型。我们可以使用关键字 typename 而不是 class 来标识函数模板中的参数，在这种情况下，模板定义如下:

```
template<typename T> T max(T x[], const int& len){
    T maximum(x[0]);
    for(int i = 0; i < len; ++i){
       if(maximum < x[i]) maximum = x[i];
    }
    return maximum;   
} 
```

Enter fullscreen mode Exit fullscreen mode

特定功能实例的创建被称为实例化。每次我们在程序中使用 **max()** 时，编译器都会检查是否已经存在与我们在函数调用中使用的参数类型相对应的函数。如果所需的函数不存在，编译器会创建一个函数，用我们在函数调用中使用的参数类型代替整个模板定义中的参数 T。

这是一个完整的函数模板的工作示例。

```
#include <iostream> #include <cstdlib>  
template<typename T> T max(T x[], const int& len){
    T maximum(x[0]);
    for(int i = 0; i < len; ++i){
       if(maximum < x[i]) maximum = x[i];
    }
    return maximum;   
}

int main(){

int small[] = {1, 24, 34, 22};
long medium[] = {23, 245, 123, 1, 234, 2345};
double large[] = {23.0, 1.4, 2.456, 345.5, 12.0, 21.0};

int lenSmall(_countof(small)); //_countof() works only on MSVC.
int lenMedium(_countof(medium));
int lenLarge(_countof(large));

std::cout << max(small, lenSmall) << "\n";
std::cout << max(medium, lenMedium) << "\n";
std::cout << max(large, lenLarge) << "\n";

return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

**例子:**

*输出:*

34

2345
345.5

对于输出数组中最大值的每个语句，使用模板实例化一个新版本的 **max()** 。当然，如果您添加另一个语句来调用函数 **max()** ，使用之前使用的类型之一，不会生成新版本的模板代码。

使用模板不会以任何方式减少你编译的程序的大小。编译器为所需的每个唯一函数生成一个版本的源代码。事实上，使用模板通常会增加程序的大小，因为即使通过强制转换参数可以满意地使用现有版本，也可能会创建函数。我们可以通过显式地包含一个声明来强制创建模板的特定实例。

模板元编程允许程序员专注于架构，并将客户端代码所需的任何实现的生成委托给编译器。因此，模板元编程可以完成真正的通用代码，有助于代码最小化和更好的可维护性。