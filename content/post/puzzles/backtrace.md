---
title: "回溯"
description: 
date: 2022-08-16T11:26:43+08:00
image: 
math: 
license: 
hidden: false
comments: true
draft: false
categories:
  - 刷题
---

# 回溯

### [全排列 II](https://leetcode.cn/problems/permutations-ii/)

```java
class Solution {
  List<List<Integer>> res;

  public void swap(int[] nums, int i, int j) {
    int tmp = nums[i];
    nums[i] = nums[j];
    nums[j] = tmp;
  }

  public void recur(int[] nums, int index) {
    if (index == nums.length) {
      List<Integer> list = new ArrayList<>();
      for (int ele : nums)
        list.add(ele);
      res.add(list);
      return;
    }
    Set<Integer> set = new HashSet<>();
    for (int i = index; i < nums.length; i++) {
      if (!set.contains(nums[i])) {
        set.add(nums[i]);
        swap(nums, index, i);
        recur(nums, index + 1);
        swap(nums, index, i);
      }
    }
  }

  public List<List<Integer>> permuteUnique(int[] nums) {
    res = new ArrayList<>();
    recur(nums, 0);
    return res;
  }
}
```

使用Set防止重复元素的操作。