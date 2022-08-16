---
title: "栈"
description: 
date: 2022-07-04T16:04:44+08:00
image: 
math: 
license: 
hidden: false
comments: true
draft: false
categories:
  - 刷题
---
## 单调栈
### [直方图最大矩形面积](https://leetcode.cn/problems/0ynMMM/)

![这是图片](../../assets/img/histogram.jpg)

**优化版本**

```java
class Solution {
  public int largestRectangleArea(int[] heights) {
    int res = 0;
    int n = heights.length;
    int[] left = new int[n];
    int[] right = new int[n];
    Arrays.fill(right, n);
    Deque<Integer> deque = new LinkedList<>();
    for (int i = 0; i < n; i++) {
      // 记录右边界
      while (!deque.isEmpty() && heights[deque.peekLast()] >= heights[i]) {
        int top = deque.removeLast();
        right[top] = i;
      }
      // 因为是单调递增的，所以左边界也确定了
      left[i] = deque.isEmpty() ? -1 : deque.peekLast();
      deque.addLast(i);
    }
    for (int i = 0; i < n; i++) {
      res = Math.max(res, heights[i] * (right[i] - left[i] - 1));
    }
    return res;
  }
}
```

```java
class Solution {
  public int valueOfArray(int[] heights, int index) {
    if (index < 0)
      return -1;
    if (index >= heights.length)
      return 0;
    else
      return heights[index];
  }

  public int largestRectangleArea(int[] heights) {
    Stack<Integer> stack = new Stack<>();
    int res = 0;
    stack.add(-1);
    int n = heights.length;
    for (int i = 0; i <= n; i++) {
      if (valueOfArray(heights, i) > valueOfArray(heights, stack.peek()))
        stack.push(i);
      else {
        while (valueOfArray(heights, i) <= valueOfArray(heights, stack.peek())) {
          int top = valueOfArray(heights, stack.pop());
          int width = i - stack.peek() - 1;
          res = Math.max(res, top * width);
        }
        stack.push(i);
      }
    }
    return res;
  }
}
```

### [接雨水](https://leetcode.cn/problems/trapping-rain-water/)

```java
//单调栈做法
public int trap(int[] height) {
  int res = 0;
  Stack<Integer> stk = new Stack<>();
  for (int i = 0; i < height.length; i++) {
    while (!stk.isEmpty()) {
      if (height[i] < height[stk.peek()])
        break;
      int index = stk.pop();
      if (stk.isEmpty())
        break;
      int minHeight = Math.min(height[i], height[stk.peek()]);
      res += (minHeight - height[index]) * (i - stk.peek() - 1);
    }
    stk.add(i);
  }
  return res;
}
```

```java
//双指针做法
class Solution {
  public int trap(int[] height) {
    int res = 0;
    int left = 0, right = height.length - 1;
    int leftMax = height[left], rightMax = height[right];
    while (left <= right) {
      while (leftMax <= rightMax && left <= right) {
        if (height[left] > leftMax) {
          leftMax = height[left];
        } else {
          res += (leftMax - height[left]);
        }
        left++;
      }
      while (leftMax > rightMax && left <= right) {
        if (height[right] > rightMax) {
          rightMax = height[right];
        } else {
          res += (rightMax - height[right]);
        }
        right--;
      }
    }
    return res;
  }
}

//稍微优雅一些的写法
public int trap(int[] height) {
  int res = 0;
  int left = 0, leftMax = height[left], right = height.length - 1, rightMax = height[right];
  while (left <= right) {
    leftMax = Math.max(leftMax, height[left]);
    rightMax = Math.max(rightMax, height[right]);
    if (leftMax > rightMax)
      res += rightMax - height[right--];
    else
      res += leftMax - height[left++];
  }
  return res;
}
```

