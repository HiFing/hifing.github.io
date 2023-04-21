---
title: "栈与队列"
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
# 栈与队列

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

## 单调队列

### [滑动窗口最大值](https://leetcode.cn/problems/sliding-window-maximum/)

```java
class Solution {
  public int[] maxSlidingWindow(int[] nums, int k) {
    Deque<Integer> deque = new LinkedList<>();
    List<Integer> res = new ArrayList<>();
    int i = 0;
    for (; i < k; i++) {
      while (!deque.isEmpty() && deque.peekLast() < nums[i])
        deque.pollLast();
      deque.addLast(nums[i]);
    }
    res.add(deque.peekFirst());
    for (; i < nums.length; i++) {
      if (deque.peekFirst() == nums[i - k])
        deque.pollFirst();
      while (!deque.isEmpty() && deque.peekLast() < nums[i])
        deque.pollLast();
      deque.addLast(nums[i]);
      res.add(deque.peekFirst());
    }
    int[] resArray = new int[res.size()];
    for (int j = 0; j < res.size(); j++) {
      resArray[j] = res.get(j);
    }
    return resArray;
  }
}
```

滑动窗口滑动的过程中势必要记录下新进来的元素，删除被挤出的元素，只不过在收入新进来的元素的时候先**保持滑动窗口中的最大值不小于这个元素**，这样一来小的元素就无需被队列记录下来了。等到这个元素出去的时候再比对队首元素与之是否相同，若相同则直接删掉就好了。遇到重复的值的时候也要记录下来，防止一个被弹出另一个不知道。

## 栈

### [最长有效括号](https://leetcode.cn/problems/longest-valid-parentheses/)

让没有用的元素垫底作为界定标准，先弹栈再计算

```java
class Solution {
  public int longestValidParentheses(String s) {
    Stack<Integer> stk = new Stack<>();
    stk.push(-1);
    int res = 0;
    for (int i = 0; i < s.length(); i++) {
      if (s.charAt(i) == '(') {
        stk.push(i);
      } else {
        stk.pop();
        if (!stk.isEmpty()) {
          res = Math.max(res, i - stk.peek());
        } else {
          stk.push(i);
        }
      }
    }
    return res;
  }
}
```

本题还可以使用DP来做，当遇到右括号的时候，记录下与之匹配的左括号的位置，倘若在这个过程中找到了之前的一个已经匹配完成的右括号，则顺着它继续往前面找，直到找到一个左括号，找不到左括号就记为0.

还可以根据贪心来做，从左边开始扫描，记录下左括号数目和右括号数目，当两者相等时，记录下此时的长度，当右括号数目大一些时，清零。因为会遇到“（（）”这种情况使得算不出结果，因此需要从右往左再进行一次相对的扫描。