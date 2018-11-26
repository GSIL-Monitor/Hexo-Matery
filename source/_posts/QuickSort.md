---
title: 快速排序(QuickSort)
date: 2018-10-31 01:04:09
copyright: true
tags: 
    - QuickSort
categories: 
    - 算法
keywords: 快速排序
description: 快速排序(Quicksort)是对冒泡排序的一种改进。快速排序由C. A. R. Hoare在1962年提出。它的基本思想是:通过一趟排序将要排序的数据分割成独立的两部分，其中一部分的所有数据都比另外一部分的所有数据都要小，然后再按此方法对这两部分数据分别进行快速排序，整个排序过程可以递归进行，以此达到整个数据变成有序序列。
author: XQ
---

###快速排序：平均时间复杂度O(N*logN)

<!-- more -->

```cpp
	
int count1 = 0;
template <class T>
int Quick_Sort_Unit_L2R(T *a, int low, int high)
{
    T pivot = a[high]; //选择第n-1个数为基准
    // pivot = a[high];||pivot = a[low]
    while (low < high)
    {
        while (a[low] <= pivot && low < high)
            low++; //从左往右找到第一个大于基准的值
        a[high] = a[low];
        count1++;
        while (a[high] >= pivot && low < high)
            high--; //从右往左找到第一个小于基准的值
        a[low] = a[high];
        count1++;
    }               //左边都小于pivot，右边都大于pivot
    a[low] = pivot; //此时low<=high
    return low;
}
template <class T>
int Quick_Scort_L2R(T *a, int low, int high)
{
    if (low >= high)
        return 0;
    int index = Quick_Sort_Unit_L2R(a, low, high);
    Quick_Scort_L2R(a, low, index - 1);
    Quick_Scort_L2R(a, index, high);
    return count1;
}

/*快速排序：平均时间复杂度O(N*logN)
 * 技巧：选择高基准，从左往右查找第一个大于基准的值，再从右往左查找第一个小于基准的值；
 * 技巧：选择低基准，从右往左查找第一个大于基准的值，再从左往右查找第一个小于基准的值**/
int count2 = 0;
template <class T>
int Quick_Sort_Unit_R2L(T *a, int low, int high)
{
    T pivot = a[low]; //选择第1个数为基准
    // pivot = a[high];||pivot = a[low]
    while (low < high)
    {
        while (a[high] >= pivot && low < high)
            high--; //从右往左找到第一个小于基准的值
        a[low] = a[high];
        count2++;
        while (a[low] <= pivot && low < high)
            low++; //从左往右找到第一个大于基准的值
        a[high] = a[low];
        count2++;
    }
    a[high] = pivot;
    return high;
}
template <class T>
int Quick_Scort_R2L(T *a, int low, int high)
{
    if (low >= high)
        return 0;
    int index = Quick_Sort_Unit_R2L(a, low, high);
    Quick_Scort_R2L(a, low, index);
    Quick_Scort_R2L(a, index + 1, high);
    return count2;
}

```
