# 来自 Javascript，Java 里的数组很混乱？

> 原文：<https://dev.to/yongliang24/coming-from-javascript-arrays-in-java-were-confusing-17la>

由于来自 Javascript，Java 中的数组让我感到困惑。下面我想讨论一下 Java 中数组的工作原理。

### 数组声明

在 Java 中，数组必须用类型声明。其中的元素也必须匹配数组类型。

```
//integer array example, both declarations will work

int [] myArray; 
int myArray [];

//string array example

String [] strArray;
String strArray []; 
```

### 给数组一个大小

在 Java 中数组的大小不会动态变化，所以我们必须在使用它之前定义一个大小。当我们需要一个动态大小的数据集合时，ArrayList 是一个更好的选择。

```
//using the arrays from the examples above, we can define a size for them

myArray = new int[10];

//we can also perform the declaration and size with one line of code

int [] myArray = new int[10]; 
```

### 数组文字

当我们知道一个数组将有哪些元素时，我们可以使用数组字面量来声明一个数组。

```
//the size of the array is determined by the number of elements were defined

int myArray = new int[]{1,2,3,4,5,6,7,8};

//in newer version of Java, we can skip the new int[]

int myArray = {1,2,3,4,5,6,7,8}; 
```

### 我最喜欢使用的循环来获取数组元素

```
for(int eachNumber : myArray){

 System.out.println(eachNumber);
} 
```