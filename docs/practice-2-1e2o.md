# 练习 2

> 原文：<https://dev.to/giladri/practice-2-1e2o>

一些你们都应该知道的小技巧:

```
// #1 - the modulo operator (just a reminder):
int x = 125;
int y = 7;
int z = 5;
int divisionRemainder = x % y; // 6
divisionRemainder = x % z; // 0

// #2 - how to get the last digit of a number:
int number = 12345;
int lastDigit = number % 10; // 5

// #3 - how to omit the last digit of a number:
int number2 = 12345;
int shorterNumber = number2 / 10; // 1234 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们写下一些代码！

## **练习#1**

写一个程序，从用户那里得到一个数字，并打印出它的数字总和。例如:

```
Enter a number:
123
The sum of the digits of the number 123 is 6 
```

Enter fullscreen mode Exit fullscreen mode

(提示:用上面的招数)。

## **练习二**

写一个程序，从用户那里得到一个整数，并检查它是否是一个质数。如果它是一个质数，程序打印“质数”，否则打印“非质数”。(提示:如果一个数不能被任何比它小的**数整除，那么它就是质数)。比如:** 

```
Enter a number:
9
Not prime

Enter a number:
3
Prime 
```

Enter fullscreen mode Exit fullscreen mode

## **练习三**

写一个程序，从用户那里得到一个整数，并建立一个星号(' * ')的金字塔，底部是这个大小。例如:

```
Insert the base size of the pyramid:
5
  *   
 *** 
*****

Insert the base size of the pyramid:
6
  **  
 **** 
******

Insert the base size of the pyramid
25
            *
           ***
          *****
         *******
        *********
       ***********
      *************
     ***************
    *****************
   *******************
  *********************
 ***********************
************************* 
```

Enter fullscreen mode Exit fullscreen mode

注意如果基数大小是偶数，金字塔的头应该是“**”，而不是“*”。

祝你好运！

#### **永远记住，事情比你想象的要复杂得多！**

问候，
吉拉德