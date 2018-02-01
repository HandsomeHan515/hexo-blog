---
title: 常用的排序算法（Python）
date: 2018-02-01 10:56:51
tags: Algorithm
categories: Algorithm
---
# 排序算法（Python）

> 排序分为内部排序和外部排序

## 内部排序

> 交换排序、选择排序、插入排序、归并排序

+ Google Chrome 对 sort 做了特殊处理。对于长度 <= 10 的数组使用的是插入排序（稳定排序）；对于长度 > 10 的的数组使用的是快速排序（不稳定排序）。

![浏览器对排序的使用](浏览器排序的使用.jpg)

### 交换排序

#### 冒泡排序（BubbleSort）

+ 基本思想：对待排序记录关键字从后往前（逆序）进行多遍扫描，当发现相邻两个关键字的次序与排序要求的规则不相符时，就将这两个记录进行交换。这样，关键字较小的记录将逐渐从后面向前面移动，就像气泡在水中向上浮一样。
+ 提升效率，当在某一遍扫描时，发现数据都已经按顺序排列了，就不再进行后续的扫描。

![冒泡排序过程](bubble_sort.jpg)

```bubble sort
def bubble_sort(arr):
    times = 0
    for i in range(len(arr) - 1):
        for j in range(len(arr) - 1 - i):
            if arr[j] > arr[j + 1]:
                arr[j], arr[j + 1] = arr[j + 1], arr[j]

    return arr


if __name__ == '__main__':
    arr = [10, 8, 4, 7, 5]
    sort_arr = bubble_sort(arr)
    print(sort_arr)

```

```bubble sort v2
def bubble_sort_v2(arr):
    for i in range(len(arr) - 1):
        pos = 0
        for j in range(len(arr) - 1 - i):
            if arr[j] > arr[j + 1]:
                arr[j], arr[j + 1] = arr[j + 1], arr[j]
                pos = 1

        if pos == 0:
            break

    return arr


if __name__ == '__main__':
    arr = arr = [10, 8, 4, 7, 5]
    sort_arr = bubble_sort_v2(arr)
    print(sort_arr)

```

#### 快速排序

+ 快速排序使用分治策略把待排序的序列分为两个子序列，具体步骤为：

1. 从数列中挑出一个元素，称该元素为“基准”；
2. 扫描一遍序列，将所有比基准小的元素排在基准前面，所有比基准大的元素排在基准后面；（该操作称为分区操作，分区操作结束后，基准元素所处的位置就是最终排序后的位置）
3. 通过递归将各子序列划分为更小的序列，直到把小于基准值元素的子数列和大于基准值元素的子数列排序。

```快速排序
def quick_sort(arr):
    # 交换元素位置
    def swap(arr, i, j):
        arr[i], arr[j] = arr[j], arr[i]

    # 分区，左小右大
    def partition(array, left, right):
        storeIndex = left
        # 基准，直接选择最右侧的
        pivot = arr[right]

        for i in range(left, right):
            if arr[i] < pivot:
                swap(arr, storeIndex, i)
                storeIndex += 1

        swap(arr, right, storeIndex)  # 将基准元素与storeIndex做交换

        return storeIndex

    def sort(arr, left, right):
        if left > right:
            return

        storeIndex = partition(arr, left, right)
        sort(arr, left, storeIndex - 1)
        sort(arr, storeIndex + 1, right)

    sort(arr, 0, len(arr) - 1)

    return arr


if __name__ == '__main__':
    arr = [10, 8, 4, 7, 5]
    sort_arr = quick_sort(arr)
    print(sort_arr)

```

### 选择排序

#### 直接选择排序

+ 基本思想：对 n 个记录进行扫描，选择最小的记录，将其输出，接着在剩下的 n-1 个记录中扫描，选择最小的将其输出...，不断的重复该过程，直到只剩一个记录为止。

![选择排序过程](selection_sort.jpg)

```selection_sort
def selection_sort(arr):
    for i in range(len(arr)):
        for j in range(i, len(arr)):
            if arr[i] > arr[j]:
                arr[i], arr[j] = arr[j], arr[i]

    return arr


if __name__ == '__main__':
    arr = [10, 8, 4, 7, 5]
    sort_arr = selection_sort(arr)
    print(sort_arr)

```

#### 堆排序

+ 基本思想

### 插入排序

#### 直接插入排序

+ 基本思想：通过构建有序序列，对于未排序数据，在已排序序列中从后向前扫描，找到相应的为止并插入。
+ 插入排序在实现上，在从后向前扫描过程中，需要反复的把已排序的元素逐步向后移动，为最新元素提供插入空间。

![直接插入排序过程](insertion_sort.jpg)

```insertion_sort
def insertion_sort(arr):
    for i in range(1, len(arr)):
        for j in range(i, 0, -1):
            if arr[j - 1] > arr[j]:
                arr[j - 1],  arr[j] = arr[j], arr[j - 1]
            else:
                break
    return arr


if __name__ == '__main__':
    arr = [10, 8, 4, 7, 5]
    sort_arr = insertion_sort(arr)
    print(sort_arr)

```

#### 希尔排序

+ 希尔排序又称为缩小增量排序
+ 基本思想：将需要排序的序列划分为若干个较小的序列，对这些序列进行直接插入排序，通过这样的操作可使需要排序的序列基本有序，最后再使用一次直接插入排序。目的是：首先对数量较小的序列进行直接插入排序可提高效率，最后对基本有序的序列进行直接插入排序，也可提高效率，从而使整个排序过程的效率得到提升。

![希尔排序过程](希尔排序.jpg)

```shell sort
def shell_sort(arr):
    gap = len(arr) // 2

    while gap > 0:
        for i in range(gap, len(arr)):
            while (i - gap) >= 0:
                if arr[i - gap] > arr[i]:
                    arr[i - gap], arr[i] = arr[i], arr[i - gap]
                    i -= gap
                else:
                    break
        gap = gap // 2

    return arr


if __name__ == '__main__':
    arr = [10, 8, 4, 7, 3]
    sort_arr = shell_sort(arr)
    print(sort_arr)

```

### 归并排序

+ 将两个或多个有序表合成一个有序表

```merge sort
def merge_sort(arr):
    if len(arr) <= 1:
        return arr

    mid = len(arr) / 2
    left = merge_sort(arr[:mid])
    right = merge_sort(arr[mid:])

    return merge(left, right)


def merge(left, right):
    sort_arr = []
    i = 0
    j = 0

    while i < len(left) and j < len(right):
        if left[i] <= right[j]:
            sort_arr.append(left[i])
            i += 1
        else:
            sort_arr.append(right[j])
            j += 1

    print(sort_arr)

    sort_arr += left[i:]
    sort_arr += right[j:]
    return sort_arr


if __name__ == '__main__':
    arr = [10, 8, 4, 7, 5]
    sort_arr = merge_sort(arr)
    print(sort_arr)

```