# PHP 中的迭代

> 原文：<https://dev.to/sannilincoln/iteration-in-php-3896>

大多数时候，当我们编写代码时，我们通常希望一个代码块在一行中反复运行。我们可以使用循环来执行这样的任务，而不是在脚本中添加几行几乎相等的代码行。

# PHP while 循环

只要指定的条件为真，while 循环就会执行一段代码。

语法是
while(条件为真){
要执行的代码；
}
例如:

```
$x = 1; 
while($x <= 5) {
    echo "The number is: $x <br>";
    $x++; 
```

上面的例子首先将变量$x 设置为 1 ($x = 1)。然后，只要$x 小于或等于 5 ($x <= 5)，while 循环就会继续运行。每次循环运行时，$x 将增加 1($ x++):

# PHP 做的...while 循环

do...while 循环将始终执行一次代码块，然后检查条件，并在指定条件为真时重复循环。
语法是:
do {
要执行的代码；
} while(条件为真)；

示例:

```
$x = 1; 
do {
    echo "The number is: $x <br>";
    $x++;
} while ($x <= 5); 
```

上面的例子首先将变量$x 设置为 1 ($x = 1)。然后，do while 循环将写入一些输出，然后将变量$x 递增 1。然后检查条件($x 是否小于或等于 5？)，只要$x 小于或等于 5，循环就会继续运行。我们应该注意，在 do while 循环中，条件是在执行完循环中的语句后测试的。这意味着 do while 循环将至少执行其语句一次，即使第一次条件为假。

# PHP 7 for 循环

当您预先知道脚本应该运行多少次时，可以使用 for 循环。
语法为:
for(init counter；测试计数器；增量计数器){
要执行的代码；
}
参数有:
init counter:初始化循环计数器值
test counter:为每次循环迭代求值。如果计算结果为真，则循环继续。如果计算结果为 FALSE，则循环结束。
增量计数器:增加循环计数器的值。
下面的例子显示了从 1 到 10 的数字

```
for ($x = 1; $x <= 10; $x++) {
    echo "The number is: $x <br>" ;
} 
```

# PHP 的 foreach 循环

foreach 循环只对数组有效，用于遍历数组中的每个键/值对。
语法
foreach ($array as $value) {
要执行的代码；
}
对于每一次循环迭代，当前数组元素的值被赋给$value，数组指针移动一位，直到到达最后一个数组元素。

下面的例子演示了一个循环，它将输出给定数组($cars)的值:

```
$cars = array("toyota", "bmw", "ford", "lexus"); 
foreach ($cars as $value) {
    echo "$value <br>";
} 
```

# PHP 嵌套循环

Php 允许将一个循环放入另一个循环中，这种类型的循环被称为嵌套循环。嵌套循环可以是 forloop、while 循环甚至 foreach 循环。嵌套 while 循环的语法是:

while(条件)
{
while(条件)
{
语句；
增量/减量；
}
语句；
递增/递减；
}

一个例子是:

```
 $i = 1 ;
 $j = 1;
while( $i < 3 )
{
    while( $j < 3 )
    {
        echo 'i love programming ';
        $j++;
    }
    echo '<br />';
    $i++;
} 
```

forloop 嵌套循环的语法是:

for(初始化；条件；
{
【增量/减量】)为(初始化；条件；【增加/减少)】
{
语句；
}
语句；
}
一个例子是:

```
 $i ;
 $j;
for( $i=1; $i<3; $i++ )
{
   for( $j=1; $j<3; $j++)
   {
      echo 'i love php';
   }
   echo '<br/>';
} 
```

在内部循环运行之前，外部循环的条件必须为真，并且可以嵌套两个以上的循环。