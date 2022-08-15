---
title: "搜索"
description: 
date: 2022-08-15T21:19:38+08:00
image: 
math: 
license: 
hidden: false
comments: true
draft: false
categories:
  - 刷题
---

# 搜索

## 二分查找

### [搜索旋转排序数组](https://leetcode.cn/problems/search-in-rotated-sorted-array/)

```java
class Solution {
  public int recur(int[] nums, int begin, int end, int target) {
    if (begin == end) {
      if (target == nums[begin])
        return begin;
      else
        return -1;
    }
    int mid = (begin + end) >> 1;
    if (nums[mid] >= nums[begin])
      if (nums[begin] <= target && target <= nums[mid])
        return Arrays.binarySearch(nums, begin, end + 1, target);
      else
        return recur(nums, mid + 1, end, target);
    else if (nums[end] >= target && target >= nums[mid])
      return Arrays.binarySearch(nums, begin, end + 1, target);
    else
      return recur(nums, begin, mid - 1, target);
  }

  public int search(int[] nums, int target) {
    int res = recur(nums, 0, nums.length - 1, target);
    return res >= 0 ? res : -1;
  }
}
```

将旋转数组分为两段，一段必然升序，若在升序段中找不到，则在另一段中再分再找。

## 并查集

### [最大人工岛](https://leetcode.cn/problems/making-a-large-island/)

先dfs把独立块的大小都计算出来存下来，再计算翻转每个0的时候能获得的联合块的大小。