# Dart 中的参数

> 原文：<https://dev.to/codingpizza/parametros-en-dart-1f11>

∞天啊！，我在最近的帖子中谈到了**变量**和**功能。**如果你丢了它们，我把它们的链接留在下面，这样你就可以看一看了。

*   **[变量](https://dev.to/codingpizza/variables-en-dart-3963)**
*   **[功能](https://dev.to/codingpizza/funciones-en-dart-19on)**

## 我们已经谈了一些他们的事。

在前面关于功能的帖子中，我们已经稍微谈了一下 ***参数*** 。我们已经看到它们作为运行“冰淇淋机”的原料。我们也可以说，参数是函数执行其程式码所需的相依性。

## 必需参数(必需参数)

所需参数是函数可以使用的最基本参数，只需指定类型名称，我们就完成了。

**所需参数**我们已经在功能**求和**中见过。

```
Integer suma(Integer a,Integer b) {
    return a+b;
} 
```

Enter fullscreen mode Exit fullscreen mode

函数中使用整数 **a** 和整数**【b】，然后函数返回两个总和的值。**

## 可选参数/可选参数

使用函数时，函数不需要可选参数。在某些情况下，可能需要将此参数传递给函数，而在另一些情况下则不需要。

为了使**成为可选参数**，必须将其置于函数签名的末尾并置于方括号内。为了更清楚地说明这一点，让我们看一下下面的例子:

```
void imprimirNombreCompleto(String nombre, String apellido, [String segundoNombre]){
    print("Mi nombre es: $name $secondName $surname");
} 
```

Enter fullscreen mode Exit fullscreen mode

在此功能中，我们可以看到我们是如何将可选参数置于**必需参数之后，**这是因为**可选参数**必须始终位于必需参数之后，否则就不会竞争

你会问，如果我们没有把它传递给函数，变量`segundoNombre`会发生什么情况呢？。变量`segundoNombre`将为 nula。而我们不想表现出“约翰零威克”？。

为了避免这种情况，我们可以添加一个缺省值，当该值为 nulo 时，将使用该值代替可选参数。

为了给一个**可选参数，**添加一个默认值，我们需要做的只是分配。为了更好地了解它，您可以查看以下示例:

```
void imprimirNombreCompleto(String nombre, String apellido, [String segundoNombre = ""]){
    print("Mi nombre es: $nombre $segundoNombre $apellido");
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，该值将为空字符串，文本将正确显示。

要将该功能与我们先前创建的可选参数配合使用，我们将执行以下操作。

```
imprimirNombreCompleto("John","Wick");
//O
imprimirNombreCompleto("Scarlett","Johansson","Ingrid"); 
```

Enter fullscreen mode Exit fullscreen mode

如果要使用函数的所有参数作为可选的“**”t1]，则可以将其括在方括号中，如下例所示。**

```
void imprimirNombreCompleto([String nombre, String segundoNombre, String Apellido]){
    print("Mi nombre es: $nombre $segundoNombre $apellido");
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 命名参数/命名参数

此类型的参数可让您告诉函数您要传递的参数。要做到这一点，我们必须把它放在钥匙之间。

示例:

```
void imprimirParametrosNombrados(String nombre,String apellido,{String segundoNombre = ""}){
    print("$nombre $segundoNombre $apellido")
} 
```

Enter fullscreen mode Exit fullscreen mode

在本示例中，我们使用了名和姓作为必需参数。和中间名为**的参数命名为**，此外，如果没有提供值，我们也确保有默认值。

当我们想要使用具有必要参数的函数时，我们必须执行下列操作:

```
imprimirParametrosNombrados("Samuel","Jackson",segundoNombre: "Leroy"); 
```

Enter fullscreen mode Exit fullscreen mode

在此范例中，我们可以看到我们需要在函数内指定参数名称，并且在两点之后指定参数值。

如果我们需要一个只有命名参数的函数，我们需要做的是把所有**参数**都包含在键内。

```
void imprimirParametrosNombrados({String nombre,String apellido,String segundoNombre = ""}){
    print("$nombre $segundoNombre $apellido")
} 
```

Enter fullscreen mode Exit fullscreen mode

当一个函数只有命名参数时，不管我们使用它们的顺序如何。

例如:

```
imprimirParametrosNombrados(nombre: "Scarlett",segundoNombre:"Ingrid",apellido:"Johansson"); 
```

Enter fullscreen mode Exit fullscreen mode

亲切，不是吗？。

## 现在轮到你了

您可以在开发环境(IDE)中测试这些概念，例如[【intellij idea communit】](https://www.jetbrains.com/idea/download/)，而且它是免费的并安装 Dart 插件，如果您喜欢 Visual Studio 代码，您也可以在其中测试这些概念，最后，如果您喜欢在线上的东西，可以使用[**darpad**](https://dartpad.dartlang.org/)

## 后置前置

如果你对更多这样的帖子感兴趣，你可以查看我关于 Dart 的其他文章。

*   **[变量](https://dev.to/codingpizza/variables-en-dart-3963)**
*   **[功能](https://dev.to/codingpizza/funciones-en-dart-19on)**

## 知道更多 Dart 剩馀？

如果你喜欢这篇帖子并且对学习 Dart 感兴趣，我现在正在电子书上写更多这样的文章，这是 Dart 的基础课程，它将帮助你掌握好知识，然后你可以用它开始浮起；页:1。你可以在这个链接中放弃**，因为电子书将是**完全免费的。****