# 基本排序

> 原文：<https://dev.to/giladri/simple-sorting-2af5>

假设我们有下面的代码:

```
#include <stdio.h>
#include <limits.h>
#include <time.h>
#include <stdlib.h> 
#define SIZE 10
#define SUCCESS 0
#define TRUE 1
#define FALSE 0 
void swap(int* x, int* y) {
    *x += *y;
    *y = *x - *y;
    *x -= *y;
}

void printArray(int* arr, int size) {
    int i = 0;
    printf("%d", arr[0]);
    for (i = 1; i < size; i++) {
        printf(", %d", arr[i]);
    }
    printf("\n");
}

void buildRandomArray(int* arr, int size) {
    // Builds a random integers array which its elements are between 0 and 100
    for (int i = 0; i < size; i++) {
        arr[i] = rand() % 100;
    }
}

int main() {
    // Random seed
    srand(time(NULL));
    // Builds a random array
    int size = SIZE;
    int* arr = (int*)malloc(size * sizeof(int));
    buildRandomArray(arr, size);

    // TODO: Sort the array <-- INSERT THE FUNCTION CALL HERE

    // Prints the array
    printArray(arr, SIZE);
    // Success
    return SUCCESS;
} 
```

Enter fullscreen mode Exit fullscreen mode

它构建一个随机的整数数组，然后打印出来。(确保你理解代码)。但是，我们希望在打印之前对数组进行排序。我们如何做到这一点？

在这篇文章中，我们将看到 3 种不同的基本算法可以完成这项任务:

## **选择排序**

在每一次迭代 I(从 0 开始)中，选择排序算法找到最小元素，并将其与当前位于数组 I 位置的元素交换。代码:

```
void selectionSort(int* arr, int size) {
    int minValue, minIndex = 0;
    for (int i = 0; i < size; i++) {
        // Sets the minimum value to be the maximum int size at the beginning of each iteration
        minValue = INT_MAX;
        for (int j = i; j < size; j++) {
            if (arr[j] <= minValue) {
                // Saves the minimum value and its index
                minValue = arr[j];
                minIndex = j;
            }
        }
        // Swaps only if the indexes are different
        if (i != minIndex) {
            swap(&arr[i], &arr[minIndex]);
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

例如:

```
45, 87, 23, 11, 43, 23, 76, 28, 18, 7
7, 87, 23, 11, 43, 23, 76, 28, 18, 45
7, 11, 23, 87, 43, 23, 76, 28, 18, 45
7, 11, 18, 87, 43, 23, 76, 28, 23, 45
7, 11, 18, 23, 43, 23, 76, 28, 87, 45
7, 11, 18, 23, 23, 43, 76, 28, 87, 45
7, 11, 18, 23, 23, 28, 76, 43, 87, 45
7, 11, 18, 23, 23, 28, 43, 76, 87, 45
7, 11, 18, 23, 23, 28, 43, 45, 87, 76
7, 11, 18, 23, 23, 28, 43, 45, 76, 87
7, 11, 18, 23, 23, 28, 43, 45, 76, 87 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，我们首先在数组的开头放置了 7，然后放置了 11，然后是 23，依此类推。

## **插入排序**

在每一次迭代 I(从 0 开始)中，插入排序选择第 I 个元素作为支点并找到它的位置。这样，在两个循环之后，数组被排序:外部循环用于选择枢轴，内部循环用于将所选枢轴放置在其位置。

```
void insertionSort(int* arr, int size) {
    int pivot;
    for (int i = 1; i < size; i++) {
        // Prints the array in each iteration
        printArray(arr, size);
        // Chooses a pivot for this iteration
        pivot = arr[i];
        // Swaps until the pivot is bigger than the j'th element
        for (int j = i - 1; j >= 0; j--) {
            if (pivot > arr[j]) {
                break;
            }
            swap(&arr[j], &arr[j + 1]);
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

例如:

```
29, 49, 48, 21, 40, 17, 58, 61, 14, 60
29, 49, 48, 21, 40, 17, 58, 61, 14, 60
29, 48, 49, 21, 40, 17, 58, 61, 14, 60
21, 29, 48, 49, 40, 17, 58, 61, 14, 60
21, 29, 40, 48, 49, 17, 58, 61, 14, 60
17, 21, 29, 40, 48, 49, 58, 61, 14, 60
17, 21, 29, 40, 48, 49, 58, 61, 14, 60
17, 21, 29, 40, 48, 49, 58, 61, 14, 60
14, 17, 21, 29, 40, 48, 49, 58, 61, 60
14, 17, 21, 29, 40, 48, 49, 58, 60, 61 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，这个例子中的第一次和第二次迭代是微不足道的，29 和 49 停留在他们的位置上。但是，第三和第四次迭代是有趣的，因为 48 在 29 和 48 之间找到了它的位置，然后 21 在数组的开始找到了它的位置，依此类推。

## **冒泡排序**

在每次迭代中，如果两个相邻元素的顺序不对，我们就在它们之间交换。这样，因为我们在循环中有一个循环，即使最小元素在数组的末尾，交换也会把它带到数组的开头。

```
void bubleSort(int* arr, int size) {
    int swapped;
    for (int i = 0; i < size; i++) {
        // Prints the array in each iteration
        printArray(arr, size);
        // Turn off the flag at the beginning of each iteration
        swapped = FALSE;
        for (int j = 0; j < size - 1; j++) {
            // Checks if the next element is bigger than the current one
            if (arr[j] > arr[j + 1]) {
                // Swaps between the two consecutive elements
                swap(&arr[j], &arr[j + 1]);
                // Turn on the flag
                swapped = TRUE;
            }
        }
        // Checks if there were any swaps in this iteration
        if (!swapped) {
            // No! the array is sorted - quits
            return;
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

例如:

```
52, 42, 91, 39, 10, 98, 68, 11, 50, 46
42, 52, 39, 10, 91, 68, 11, 50, 46, 98
42, 39, 10, 52, 68, 11, 50, 46, 91, 98
39, 10, 42, 52, 11, 50, 46, 68, 91, 98
10, 39, 42, 11, 50, 46, 52, 68, 91, 98
10, 39, 11, 42, 46, 50, 52, 68, 91, 98
10, 11, 39, 42, 46, 50, 52, 68, 91, 98
10, 11, 39, 42, 46, 50, 52, 68, 91, 98 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，我们用 42 换了 52，然后用 52 换了 39，再用 52 换了 10，依此类推。试着用笔和纸跟随这个算法，并确保你完全理解它。注意，由于我们在基本算法中添加了 **`swapped`** 标志，一旦数组排序完毕，算法就会停止。例如，如果数组被排序，冒泡排序算法(带有这个标志)在一次迭代后停止——这是检查数组是否被排序的最佳时机。上述所有其他算法将完全免费运行他们的所有迭代。这使得冒泡排序算法成为最好的算法，如果数组可以排序的概率很高。

#### **永远记住，事情比你想象的要复杂得多！**

问候，
吉拉德