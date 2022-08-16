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

### [最长递增子序列](https://leetcode.cn/problems/longest-increasing-subsequence/)

```java
// O(n^2)的方法为dp，下面用二分优化达到O(nlogn)级别
class Solution {
  public int lengthOfLIS(int[] nums) {
    int n = nums.length;
    int[] record = new int[n];
    int index = 0;
    for (int ele : nums) {
      int l = 0, r = index;
      while (l < r) {
        int mid = (l + r) >> 1;
        if (ele > record[mid])
          l = mid + 1;
        else
          r = mid;
      }
      record[l] = ele;
      if (l == index)
        index++;
    }
    return index;
  }
}
```

record的长度就是结果，在过程中更新record中的元素，使之越来越小以满足后面的元素插入数组来拓宽长度，这不影响之前获得的临时最长递增子序列长度的结果。

### [寻找两个正序数组的中位数](https://leetcode.cn/problems/median-of-two-sorted-arrays/)

```java
class Solution {
  public double findMedianSortedArrays(int[] nums1, int[] nums2) {
    int[] less;
    int[] more;
    if (nums1.length < nums2.length) {
      less = nums1;
      more = nums2;
    } else {
      less = nums2;
      more = nums1;
    }
    int n1 = less.length;
    int n2 = more.length;
    int left = 0, right = less.length;
    // 右边会持有单独的中位数
    while (left < right) {
      int mid = (left + right) / 2;
      int secondIndex = (n1 + n2 - 2 * mid) / 2;
      if (mid - 1 >= 0 && secondIndex < n2 && less[mid - 1] > more[secondIndex])
        right = mid - 1;
      else if (secondIndex - 1 >= 0 && mid < n1 && less[mid] < more[secondIndex - 1])
        left = mid + 1;
      else
        break;
    }
    double res = 0;
    int one = (left + right) / 2;
    int two = (n1 + n2 - 2 * one) / 2;
    if ((n1 + n2) % 2 == 1) {
      // 奇数取右边
      res = Math.min((one == n1 ? Integer.MAX_VALUE : less[one]), (two == n2 ? Integer.MAX_VALUE : more[two]));
    } else {
      double tmpLeft = Math.max((one == 0 ? Integer.MIN_VALUE : less[one - 1]),
          (two == 0 ? Integer.MIN_VALUE : more[two - 1]));
      double tmpRight = Math.min((one == n1 ? Integer.MAX_VALUE : less[one]),
          (two == n2 ? Integer.MAX_VALUE : more[two]));
      res = (tmpLeft + tmpRight) / 2;
    }
    return res;
  }
}
```

找到两个数组的index，使得这两个index左侧的数都小于右侧的数。最终结果根据奇偶性分开处理。

## 并查集

### [最大人工岛](https://leetcode.cn/problems/making-a-large-island/)

先dfs把独立块的大小都计算出来存下来，再计算翻转每个0的时候能获得的联合块的大小。