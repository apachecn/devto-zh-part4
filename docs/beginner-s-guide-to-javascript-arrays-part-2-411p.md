# Javascript 数组初学者指南[第 2 部分]

> 原文：<https://dev.to/ogurinkaben/beginner-s-guide-to-javascript-arrays-part-2-411p>

嗨伙计们！首先，我对这个职位的前任受到的接待感到非常震惊。真的很感激。如果你是第一次看这篇文章，一定要点击这里查看这篇文章的第一部分。。

对于这个帖子，我们将考虑以下方法。

*   减少()
*   地图()
*   过滤器()
*   排序()

让我们开始吧。

首先，让我们定义一个(或两个)我们将用于此的数组。

```
 // Array 1

const students = [

{firstname: 'Tammy', lastname:'Benjamin', admitted: 2011, graduation: 2014},
{firstname: 'Deborah', lastname:'Emeni', admitted: 2017, graduation: 2021},
{firstname: 'Daniel', lastname:'Oke', admitted: 2009, graduation: 2012},
{firstname: 'Ogurinka', lastname:'Benjamin', admitted: 2012, graduation: 2016},
{firstname: 'Nelson', lastname:'Abel', admitted: 2017, graduation: 2021},
{firstname: 'Ellie', lastname:'Julie', admitted: 2013, graduation: 2017},

];

// Array 2

const teachers = [

'Stephen', 'Richard', 'Biodun', 'Ruth',
'Peter', 'Paul', 'John', 'Jacob', 'Chris',
'Emeka', 'Nicole', 'Jaden', 'Albert',

]; 
```

Enter fullscreen mode Exit fullscreen mode

数组 1 保存了一个指定姓名、入学年份和毕业年份的对象数组(在这里阅读对象[)。](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object)

数组 2 保存教师的基本数组。

假设我们想从数组中获取 2017 年录取的学生名单，这可以使用 ***filter()*** 方法来实现

## 什么是 filter()方法？

filter()方法使用通过测试的数组元素创建一个新数组。这对于访问对象的属性很有用。如果当前项通过条件，它将被发送到新数组。在上面的练习中，我们希望获得对象数组中特定属性的值，在本例中是 2017 年录取的每个学生

代码:

```
 const admittedIn2017 = students.filter(function(student) {

if (student.admitted === 2017) {
return true;

}

});

console.log(admittedIn2017); 
```

Enter fullscreen mode Exit fullscreen mode

解释:

上面的代码遍历整个数组，检查每个值为 2017 的事件，然后将显示的对象返回到控制台。我们获取学生数组并对其进行过滤。filter 方法接受一个带有参数的函数。我们使用这个参数来引用我们的对象中的属性(在这个例子中是“accepted”)，然后设置我们的条件。每当条件通过时，该函数返回 true。

挑战:

作为一个个人挑战，添加更多的数组项，并尝试返回将在一个年份范围内(比如 2012 年到 2021 年之间)毕业的每个学生

## 地图()

假设您想要获得数组中每个学生的名字和入学年份，我们可以使用 ***map()*** 方法来实现。

map()方法通过对每个数组元素执行一个函数来创建一个新数组。在我们的例子中，我们希望获得每个学生的名字和毕业年份。需要注意的是，新数组的长度与原始数组的长度相同。

代码:

```
 const firstNameAndGradYear = students.map(function(student) {

return student.firstname + "  " + student.graduation;

});

console.log(firstNameAndGradYear); 
```

Enter fullscreen mode Exit fullscreen mode

解释:

在这里，我们迭代我们的学生数组，返回每个对象的“名字”和“毕业”的值，然后将其显示到我们的控制台。

挑战:

尝试返回每个学生的姓和名。(相当简单)

## 排序()

***sort()*** 方法根据一定的条件对数组进行排序，并返回一个新的数组。

假设我们希望根据学生的毕业年份对他们的数组进行排序

代码:

```
 const aphabetically = students.sort(function(a, b) {

if (a.firstname > b.firstname) {

  return 1;
}
else {

return -1;
}

});

console.log (aphabetically); 
```

Enter fullscreen mode Exit fullscreen mode

解释:

上面的代码遍历了我们的学生数组，并比较了所有的名字。它根据名字的字母顺序检查和排序我们的学生数组。它接受两个参数(a，b ),并根据字母顺序上下移动

挑战:

尝试根据毕业年份顺序对数组进行排序。

## 减少()

***reduce()*** 方法在每个数组元素上运行一个函数，将其缩减为一个值。

为了更好地理解这一点，我们将尝试验证我们数组中的所有学生将在学校度过多少年。

代码:

```
 const yearInSchool = students.reduce(function(total, student) {

return total + (student.graduation - student.admitted)

}, 0);

console.log(yearInSchool); 
```

Enter fullscreen mode Exit fullscreen mode

解释:

我们遍历数组，输出数组中所有学生将在学校度过的总年数。这里的“total”参数作为一个累加器，我们赋予它一个初始值 0，每次迭代数组时，它都被赋予数学运算的值(student . graduation-student . admitted)。你可以在这里阅读更多关于它和它是如何工作的！

挑战:

试着得出 2021 年毕业的所有学生将在学校度过的年数

## 附加数组方法

还有一些其他的数组方法在不同的情况下也是有用的

### toString()

这会将数组转换为字符串。

用法:(使用我们的教师数组)

```
 teachers.toString() // Converts the teachers array to string separated by a comma 
```

Enter fullscreen mode Exit fullscreen mode

### 加入()

它的行为就像 toString()，但是除此之外，您还可以指定分隔符

用法:

```
 teachers.join('/'); // Converts the teachers array to string separated by a forward slash 
```

Enter fullscreen mode Exit fullscreen mode

### 每()

every()方法检查是否所有数组值都通过了测试。

示例:使用我们的学生数组

例如，我们将尝试检查我们的学生数组的所有入学年份是否都大于 2011 年

```
 const greaterThan2011 = students.every(function(student) {
return student.admitted >= 2011;
});
console.log(greaterThan2011); // will return false 
```

Enter fullscreen mode Exit fullscreen mode

### 有的()

some()方法检查一些数组值是否通过了测试。

示例:使用我们的学生数组

```
 const greaterThan2011 = students.some(function(student) {
return student.admitted >= 2011;
});
console.log(greaterThan2011); // will return true 
```

Enter fullscreen mode Exit fullscreen mode

### 找到()

find()方法返回通过测试函数的第一个数组元素的值

示例:使用我们的学生数组

```
 const greaterThan2011 = students.find(function(student) {
return student.admitted >= 2011;
});
console.log(greaterThan2011); 
```

Enter fullscreen mode Exit fullscreen mode

### indexOf()

indexOf()方法在数组中搜索元素值并返回其位置。它需要两个参数，要搜索的项目(这是必需的)和从哪里开始搜索。负值将从给定位置开始，从末尾开始计数，并搜索到末尾(这是可选的)

示例:使用我们的教师数组

```
 console.log(teachers.indexOf("Biodun")); // returns 2 
```

Enter fullscreen mode Exit fullscreen mode

### lastIndexOf()

这与 indexOf()相同，但返回指定元素最后一次出现的位置。

### 包括()

includes()方法确定数组是否包含指定的元素。

示例:使用我们的教师数组

```
 console.log(teachers.includes("Biodun")); // returns true 
```

Enter fullscreen mode Exit fullscreen mode

## 总结

根据您要完成的任务，还有许多有用的数组方法。我已经花时间列出了我有理由使用的方法，我希望你能从中得到一些东西。谢谢你。