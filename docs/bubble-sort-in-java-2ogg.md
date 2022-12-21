# Java 中的冒泡排序

> 原文：<https://dev.to/l0l0l0l/bubble-sort-in-java-2ogg>

冒泡排序是最简单的排序算法之一，非常直观易懂。我们比较相邻的元素，看看它们的顺序是否错误(a[i] > a[j] for 1 <= i < j <= size of array; if array is to be in ascending order, and vice-versa). If yes, then swap them.

```
Implementation of Bubble Sort in Java:
import java.util.Scanner;
class BubbleSort {
  public static void main(String []args) {
    int n;
    Scanner in = new Scanner(System.in);
 System.out.println("Input number of integers to sort");
    n = in.nextInt();
    int array[] = new int[n];
     System.out.println("Enter " + n + " integers");
     for (int i = 0; i < n; i++)
      array[i] = in.nextInt();

    for (int i = 0; i < n - 1; i++) {
   Boolean swapped = false;
      for (int j = 0; j < n - i - 1; j++) {
        if (array[j] > array[j+1]) /* For descending order use < */
        {
          int temp = array[j];
          array[j]= array[j+1];
          array[j+1] = temp;

      swapped = true;
        }
      }
   if(!swapped)
      break;
   } 
System.out.println("Sorted list of numbers:");
for (int i = 0; i < n; i++)
System.out.println(array[i]);
  }
} 
```

最初发布于[面试位](https://www.interviewbit.com/tutorial/bubble-sort/)。