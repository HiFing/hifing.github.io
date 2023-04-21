---
title: "哈希"
description: 
date: 2022-08-18T00:21:42+08:00
image: 
math: 
license: 
hidden: false
comments: true
draft: false
categories:
  - 刷题
---

# 哈希

## HashSet

### [最长连续序列](https://leetcode.cn/problems/longest-consecutive-sequence/)

```java
class Solution {
  public int longestConsecutive(int[] nums) {
    Set<Integer> set = new HashSet<>();
    for (int ele : nums)
      set.add(ele);
    int res = 0;
    for (int ele : nums) {
      if (!set.contains(ele - 1)) {
        int count = 1;
        int tmp = ele + 1;
        while (set.contains(tmp)) {
          count++;
          tmp++;
        }
        res = Math.max(res, count);
      }
    }
    return res;
  }
}
```

将元素都收到Set中，从连续序列的最小元素开始搜索到最大处，如果还有连续的更小的，则放弃此次循环

## HashMap

### [和为 K 的子数组](https://leetcode.cn/problems/subarray-sum-equals-k/)

将前缀记录在map里头，往后边扫描边查缺的数字。