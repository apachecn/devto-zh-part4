# 列表与数组数组与列表与数组列表

> 原文：<https://dev.to/fihra/arrays-vs-lists-vs-arraylists-1da4>

在经历了一次编码训练营之后，我大部分时间都在使用 Ruby 和 Javascript，后来我发现使用这两种语言的数组并不是真正意义上的“传统”数组。我开始学习 C#并注意到数组的构建不同于 Ruby & Javascript。我们在这两种语言中使用的“数组”更像是一个“列表”，或者更像是一个“数组列表”让我们探讨一下这三者的区别，看看哪一个适合根据具体情况来使用。

## 数组

#### ‘红宝石中’

```
empty_array = []
num_array = [2, 6, 25, 100]
random_array = ['words', 'house', 10, true, 7, nil]

num_array.push(7, 5, 'something') 
#num_array << 7 << 5 << 'something' #works the same way
#num_array output: [2, 6, 25, 100, 7, 5, 'something'] 
```

Enter fullscreen mode Exit fullscreen mode

#### '在 Javascript 中'

```
const emptyArray = []
const numArray = [2, 6, 25, 100]
const randomArray = ['words', 'house', 10, true, 7, null]

numArray.push(7, 5, 'something')
//numArray output: [2, 6, 25, 100, 7, 5, 'something'] 
```

Enter fullscreen mode Exit fullscreen mode

#### ‘在 C#中’

```
int[] emptyNumArray = new int[0];
int[] numArray = new int[] {2, 6, 25, 100};
//int[] numArray = {2, 6, 25, 100}; //Alternate to above
string[] stringArray = {'words', 'something', 'stuff'};

string[] randomArray = {'words', 'house', 10, true, 7, null};
//This returns an error

//No method for pushing, or appending to an array 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以看到 Ruby 和 Javascript 在处理数组的方式上非常相似和灵活。您可以创建空数组，将新对象推入数组，更具体地说，可以将几乎任何东西推入数组。C#似乎对数组的构造方式很挑剔。让我们浏览每一行，看看 C#中发生了什么。

```
int[] emptyNumArray = new int[0]; 
```

Enter fullscreen mode Exit fullscreen mode

这实际上是一个非常无用的数组，因为我们确实在创建一个空数组，但是我们给了它 0 长度的槽。如果我们把它改成至少一个长度:

```
int[] emptyNumArray = new int[5];

for(int i=0; i < emptyNumArray.Length; i++){
   emptyNumArray[i] = i;
}
//Then we have to append integers to each slot of the array. 
```

Enter fullscreen mode Exit fullscreen mode

这表明数组在这种意义上是锁定的，没有提供附加到容器的灵活性。还要注意有这个' int[]. '这也将数组锁定为只允许该数组中的整数值，这就是为什么我们之前看到 C#中的“string[] randomArray”会产生错误。

不过，我确实发现 Ruby 和 Javascript 的一个特点是，你可以实例化一个固定大小的数组。

红宝石

```
array = Array.new(5) #array with a length of 5 
```

Enter fullscreen mode Exit fullscreen mode

Javascript

```
const newArr = Array(5).fill(null).map((x, i) => i); 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们已经看到了使用数组的语言之间的一些差异，让我们来理解数组到底是什么。

数组是一种数据结构，它可以存储相同数据类型的固定大小的元素集合。数组是用来存储数据集合的，但是将数组看作来自 Tutorialspoint 的同类型变量的集合通常更有用

等等，那我们如何描述 Ruby 和 Javascript 的‘数组？’我们可以把它们描述成一个动态数组，因为它是可增长、可调整大小和可变的。

所以与动态相反的是一个静态数组。在这个链接中，亨利·菲尔德将静态数组定义为“需要我们在编译时知道大小的数组。”我们可以这样想，我们必须定义数组的大小，它是不可变的，主要是在适当的位置有锁定的值。

这都是由于堆和堆栈内存分配。这是本博客要讨论的全部内容，所以我将提供每种语言如何处理内存分配的链接:

【Ruby 如何使用内存
[JavaScript 堆栈大小](https://glebbahmutov.com/blog/javascript-stack-size/)
[C#堆(ing) Vs 栈(ing) In。NET - Part One](https://www.c-sharpcorner.com/article/C-Sharp-heaping-vs-stacking-in-net-part-i/) 

但是让我们继续，那么我们如何用 C#来解决这个问题呢？介绍列表！

## 列表

"列表是一个以特定顺序保存变量的对象."- [来源](https://www.learncs.org/en/Lists)

回想起来，这就是我们可以在 C#中使用的 Ruby 和 Javascript 中的动态数组。

```
//You'll have to import a method library to use the List syntax
using System.Collections.Generic;

List<int> listOfNumbers = new List<int>();

listOfNumbers.Add(2);
listOfNumbers.Add(6);
listOfNumbers.Add(25);
listOfNumbers.Add(100);

//listOfNumbers output: {2, 6, 25, 100};

//You can also instantiate from the beginning
List<int> listOfNumbers2 = new List<int>{2, 6, 25, 100}; 
```

Enter fullscreen mode Exit fullscreen mode

在 C#中，列表通常比数组用得多，但是在某些情况下数组是必要的，比如如果你的数据不应该急剧增长。

这里有一个链接，可以看到 C#中数组和列表的区别。

[C#数组 vs 列表](https://www.educba.com/c-sharp-array-vs-list/)

最后一部分，数组列表！

## 数组列表

在 C#中，数组列表是一种非泛型类型的集合。它可以包含任何数据类型的元素。它类似于一个数组，只不过它会随着您向其中添加项目而自动增长。不像数组，不需要指定 ArrayList 的大小。”——[来源](https://www.tutorialsteacher.com/csharp/csharp-arraylist)

```
//Import syntax library to use ArrayList
using System.Collections;

ArrayList newList = new ArrayList();

newList.Add("A");
newList.Add(4);
newList.Add(false);

//newList output: {"A", 4, false}; 
```

Enter fullscreen mode Exit fullscreen mode

因此，由于数据类型的限制，Arraylist 看起来比 list 更灵活。列表被认为是“泛型”集合，而数组列表是“非泛型”集合，这是不寻常的。Arraylists 主要创建一个“Object”类型的集合，将其他类型的变量转换成一个对象，这与在实例化 list 时坚持某种数据类型的 list 相反。

这就是我对数组，列表和数组列表的全部了解。有许多资源讨论了这些数据结构的差异，因为不同的语言如何利用它们的数据结构，我们比较了 Ruby、Javascript 和 C#的差异。我希望这是一个有用的博客，介绍了从不同的角度看这些数据结构。