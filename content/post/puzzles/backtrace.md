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

### [缺失的第一个正数](https://leetcode.cn/problems/first-missing-positive/)

```java
class Solution {
  public int firstMissingPositive(int[] nums) {
    int tmp = 0;
    for (int i = 0; i < nums.length;) {
      if (nums[i] == i + 1 || nums[i] > nums.length || nums[i] < 1) {
        i++;
        continue;
      }
      tmp = nums[i];
      if (nums[i] == nums[nums[i] - 1]) {
        i++;
        continue;
      }
      nums[i] = nums[nums[i] - 1];
      nums[tmp - 1] = tmp;
    }
    for (int i = 0; i < nums.length; i++) {
      if (nums[i] != i + 1)
        return i + 1;
    }
    return nums.length + 1;
  }
}
```

运用置换的方法，可以实现O(n)的时间复杂度。 因为当数组每一个位置都正确地放置了该放置的数之后，最小的正数会是数组之外的数，若是数组还有成员没有被正确放置，那么他可能就是答案。

要注意可能会有无限循环的情况，比如出现重复的数，这个时候只要跳出去本次遍历直接处理下一个就好了。