---
title: "计数"
description: 
date: 2022-08-19T01:04:20+08:00
image: 
math: 
license: 
hidden: false
comments: true
draft: false
categories:
  - 刷题
---

# 计数

## 投票

### [多数元素](https://leetcode.cn/problems/majority-element/)

```java
class Solution {
  public int majorityElement(int[] nums) {
    int now = nums[0];
    int count = 1;
    for (int i = 1; i < nums.length; i++) {
      if (nums[i] == now)
        count++;
      else {
        if (count > 0)
          count--;
        else {
          count = 1;
          now = nums[i];
        }
      }
    }
    return now;
  }
}
```

使用摩尔投票法将复杂度将为O（n），也可以用分治的思想，左右选出的候选人再在整个区间内进行比较，数目多的取胜，复杂度为O（nlogn）。