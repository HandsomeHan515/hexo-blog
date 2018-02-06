---
title: 常见的查找算法（Python）
date: 2018-02-05 16:44:22
tags: Algorithm
categories: Algorithm
---
# 常见的查找算法（Python）

## 顺序查找

- 平均时间复杂度 O（n）

```order
def order_search(key, array):
    length = len(array)
    if array == None or not length:
        return "key 不存在"

    for i in range(length):
        if (array[i] == key):
            return i

    return "key 不存在"


if __name__ == '__main__':
    arr = [1, 3, 4]
    search = order_search(10, arr)
    print('search: {}'.format(search))

```

## 二分查找

- 时间复杂度 O(log2n)
- 二分查找又称折半查找，他是一种效率较高的查找方式。
- 要求：必须采用顺序存储结构，必须按关键字大小有序排列

![折半查找](binary.jpg)

```binary
def binary_search(key, array):
    low = 0
    high = len(array) - 1

    while(low <= high):
        middle = (low + high) // 2
        if key == array[middle]:
            return middle
        elif key < array[middle]:
            high = middle - 1
        else:
            low = middle + 1

    return None


if __name__ == '__main__':
    arr = [1, 3, 4, 5, 7, 9]
    search = binary_search(3, arr)
    print('search: {}'.format(search))
```

## 分块查找

- 算法思想：将n个数据元素”按块有序”划分为m块（m ≤ n）。每一块中的结点不必有序，但块与块之间必须有序；即第1块中任一元素的关键字都必须小于第2块中任一元素的关键字；而第2块中任一元素又都必须小于第3块中的任一元素...