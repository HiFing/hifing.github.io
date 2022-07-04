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
...
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

