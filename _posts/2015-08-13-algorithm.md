---
layout: post
title: 算法和数据结构
category: title
---

> 数据结构

####1. 堆(二叉堆)

参考： 

* [堆 (数据结构)](https://zh.wikipedia.org/wiki/%E5%A0%86_(%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84) "markdown")
* [数据结构和算法学习笔记：优先队列和堆](http://www.yeolar.com/note/2012/05/26/ds-heap/ "markdown")


> 算法

####1. 二分查找

```
// while循环
int binary_search(const int arr[], int start, int end, int key) {
	int mid;
	while (start <= end) {
		mid = start + (end - start) / 2; //直接平均可能會溢位，所以用此算法
		if (arr[mid] < key)
			start = mid + 1;
		else if (arr[mid] > key)
			end = mid - 1;
		else
			return mid; //最後檢測相等是因為多數搜尋狀況不是大於要不就小於
	}
	return -1;
}
```

参考： [折半搜索算法](https://zh.wikipedia.org/zh-cn/%E6%8A%98%E5%8D%8A%E6%90%9C%E7%B4%A2%E7%AE%97%E6%B3%95 "markdown")

####2. 快速排序

```
//递归法
void swap(int *x, int *y) {
	int t = *x;
	*x = *y;
	*y = t;
}
void quick_sort_recursive(int arr[], int start, int end) {
	if (start >= end)
		return;
	int mid = arr[end];
	int left = start, right = end - 1;
	while (left < right) {
		while (arr[left] < mid && left < right)
			left++;
		while (arr[right] >= mid && left < right)
			right--;
		swap(&arr[left], &arr[right]);
	}
	if (arr[left] >= arr[end])
		swap(&arr[left], &arr[end]);
	else
		left++;
	quick_sort_recursive(arr, start, left - 1);
	quick_sort_recursive(arr, left + 1, end);
}
void quick_sort(int arr[], int len) {
	quick_sort_recursive(arr, 0, len - 1);
}
```

参考： [快速排序](https://zh.wikipedia.org/zh-cn/%E5%BF%AB%E9%80%9F%E6%8E%92%E5%BA%8F "markdown")

####3. 归并排序

```
//递归版
void merge_sort_recursive(int arr[], int reg[], int start, int end) {
	if (start >= end)
		return;
	int len = end - start, mid = (len >> 1) + start;
	int start1 = start, end1 = mid;
	int start2 = mid + 1, end2 = end;
	merge_sort_recursive(arr, reg, start1, end1);
	merge_sort_recursive(arr, reg, start2, end2);
	int k = start;
	while (start1 <= end1 && start2 <= end2)
		reg[k++] = arr[start1] < arr[start2] ? arr[start1++] : arr[start2++];
	while (start1 <= end1)
		reg[k++] = arr[start1++];
	while (start2 <= end2)
		reg[k++] = arr[start2++];
	for (k = start; k <= end; k++)
		arr[k] = reg[k];
}
void merge_sort(int arr[], const int len) {
	int* reg = (int*)malloc(sizeof(int) * len);
	merge_sort_recursive(arr, reg, 0, len - 1);
	free(reg);
}
```

参考： [归并排序](https://zh.wikipedia.org/zh-cn/%E5%BD%92%E5%B9%B6%E6%8E%92%E5%BA%8F "markdown")

####4. 堆排序

参考： [堆排序](https://zh.wikipedia.org/zh-cn/%E5%A0%86%E6%8E%92%E5%BA%8F "markdown")

####5. 时间复杂度表

![时间复杂度](http://img.kuqin.com/upimg/allimg/150316/2135526204-41.jpg "markdown")

归并排序的时间复杂度分析如下：

![归并排序时间复杂度](http://img154.ph.126.net/rl0iR6_hFUqL_BX2CKcR_Q==/1482810177313103736.png "merge")
