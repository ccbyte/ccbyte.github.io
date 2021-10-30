---
date: 2021-07-09
title: "Git分支"
description: "Git分支的使用方法，创建/删除/修改/切换"
tags: ["git"]
categories: ["git"]
---
# 二分法

## 二分查找
基于有序序列的查找方法，时间复杂度为**O(logn)**

``` c
#include<cstdio>

int binarySearch(int A[], int left, int right, int x) {
    int mid; // left和right中点
    while (left <= right) {
        mid = (left + right) / 2; // 取中点
        if(A[mid] == x) return mid;
        else if(A[mid] > x) right = mid - 1;
        else left = mid + 1; 
    }
    return -1; // 查找失败返回-1
}

int main() {
    const int n = 10;
    int A[n] = { 1, 3, 4, 6, 7, 8, 10, 11, 12, 15 };
    printf("%d, %d\n", binarySearch(A, 0, n-1, 6), binarySearch(A, 0, n-1, 9));
    return 0;
}
```
## 二分法拓展


## 快速幂

