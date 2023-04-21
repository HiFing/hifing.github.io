---
title: "贪心"
description: 
date: 2022-08-17T22:13:57+08:00
image: 
math: 
license: 
hidden: false
comments: true
draft: false
categories:
  - 刷题
---

# 贪心



### 相关题目：[下一个排列](https://leetcode.cn/problems/next-permutation/)

```java
class Solution {
  public void reverse(int[] nums, int l, int r) {
    int tmp;
    while (l < r) {
      tmp = nums[l];
      nums[l] = nums[r];
      nums[r] = tmp;
      l++;
      r--;
    }
  }

  public void nextPermutation(int[] nums) {
    int i = nums.length - 1;
    for (; i > 0; i--) {
      if (nums[i] > nums[i - 1])
        break;
    }
    if (i == 0)
      reverse(nums, 0, nums.length - 1);
    else {
      for (int j = nums.length - 1; j > i - 1; j--) {
        if (nums[j] > nums[i - 1]) {
          int tmp = nums[j];
          nums[j] = nums[i - 1];
          nums[i - 1] = tmp;
          reverse(nums, i, nums.length - 1);
          return;
        }
      }
    }
  }
}
```

从尾到头找到一个极大值点，在极值点右侧这个递减序列中从右往左找到极值点左侧元素的位置，使得左侧元素刚好比这个位置的元素大，替换后，翻转右侧序列。