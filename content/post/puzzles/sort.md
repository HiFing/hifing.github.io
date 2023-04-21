---
title: "排序"
description: 
date: 2022-07-04T16:46:07+08:00
image: 
math: 
license: 
hidden: false
comments: true
draft: false
categories:
  - 刷题
---
# 排序

## 快速排序

### 模板题

```java
class Solution {
  private void swap(int[] nums, int a, int b) {
    int tmp = nums[a];
    nums[a] = nums[b];
    nums[b] = tmp;
  }

  private void fastSort(int[] nums, int l, int r) {
    if (l >= r)
      return;
    // 也可以直接认定pivot为左端点
    int start = l, end = r;
    swap(nums, start, (start + end) / 2);
    // 指针重合时退出
    while (start < end) {
      // 先动右指针，避免指针重复时左指针指向超过pivot的数字
      while (start < end && nums[end] >= nums[l])
        end--;
      while (start < end && nums[start] <= nums[l])
        start++;
      swap(nums, start, end);
    }
    // 交换左指针和pivot的位置
    swap(nums, l, start);

    fastSort(nums, l, start - 1);
    fastSort(nums, start + 1, r);
  }

  public int[] sortArray(int[] nums) {
    fastSort(nums, 0, nums.length - 1);
    return nums;
  }
}
```

### [出现频率最高的 k 个数字](https://leetcode.cn/problems/g5c51o/)(TOPK)

 topK(无序：小顶堆、单分支快排)

快排变体（单分支快排），当然，本题用**堆**来做思路非常清晰

```java
  public int[] topKFrequent(int[] nums, int k) {
    Map<Integer, Integer> map = new HashMap<>();
    for (int num : nums) {
      map.put(num, map.getOrDefault(num, 0) + 1);
    }
    List<int[]> values = new ArrayList<>();
    for (Map.Entry<Integer, Integer> entry : map.entrySet()) {
      int num = entry.getKey(), count = entry.getValue();
      values.add(new int[] { num, count });
    }
    findK(k, values);
    int[] ret = new int[k];
    for (int i = 0; i < k; i++)
      ret[i] = values.get(i)[0];
    return ret;
  }

  public void findK(int k, List<int[]> values) {
    int l = 0;
    int n = values.size();
    int r = n - 1;
    while (l < r) {
      int index = qsort(l, r, values);
      if (index == k - 1)
        return;
      else if (index < k - 1)
        l = index + 1;
      else
        r = index - 1;
    }
  }

  public int qsort(int l, int r, List<int[]> values) {
    int pivot = values.get(l)[1];
    int start = l;
    while (l < r) {
      while (l < r && values.get(r)[1] <= pivot)
        r--;
      while (l < r && values.get(l)[1] >= pivot)
        l++;
      if (l < r)
        Collections.swap(values, l, r);
    }
    Collections.swap(values, start, r);
    return r;
  }
```

## 堆排序

### [手撕堆排序](https://leetcode.cn/problems/sort-an-array)

```java
class Solution {
  public void dropDown(int[] nums, int index, int limit) {
    if (index * 2 + 1 >= limit)
      return;
    boolean flag = false;
    int change = index;
    if (nums[index] < nums[index * 2 + 1]) {
      flag = true;
      change = index * 2 + 1;
    }
    if (index * 2 + 2 < limit && nums[change] < nums[index * 2 + 2]) {
      flag = true;
      change = index * 2 + 2;
    }
    if (flag) {
      int tmp = nums[change];
      nums[change] = nums[index];
      nums[index] = tmp;
      dropDown(nums, change, limit);
    }
  }

  public int[] sortArray(int[] nums) {
    int start = (nums.length - 2) / 2;
    for (; start >= 0; start--) {
      dropDown(nums, start, nums.length);
    }
    for (int i = nums.length - 1; i > 0; i--) {
      int tmp = nums[i];
      nums[i] = nums[0];
      nums[0] = tmp;
      dropDown(nums, 0, i);
    }
    return nums;
  }
}
```

