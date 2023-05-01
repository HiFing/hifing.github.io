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

### [在排序数组中查找元素的第一个和最后一个位置](https://leetcode.cn/problems/find-first-and-last-position-of-element-in-sorted-array/)

```java
class Solution {
  public int[] searchRange(int[] nums, int target) {
    if (nums.length == 0) return new int[]{ -1, -1 };
    int l = 0, r = nums.length - 1;
    while (l < r) {
      // 位运算优先级低，要用括号包起来
      int mid = l + ((r - l) >> 1);
      if (nums[mid] >= target)
        r = mid;
      else
        l = mid + 1;
    }
    int low = -1;
    if (nums[l] == target)
      low = l;
    l = 0;
    r = nums.length - 1;
    while (l < r) {
      // 补一个1，让mid倾向于右边的元素
      int mid = l + ((r - l + 1) >> 1);
      if (nums[mid] > target)
        r = mid - 1;
      else
        l = mid;
    }
    int high = -1;
    if (nums[l] == target)
      high = l;
    return new int[] { low, high };
  }
}
```

## 并查集

### [最大人工岛](https://leetcode.cn/problems/making-a-large-island/)

先dfs把独立块的大小都计算出来存下来，再计算翻转每个0的时候能获得的联合块的大小。

```java
// 一种省事一些的并查集方法
// 还没有编写反转0之后的逻辑
public class Main {
  int[] dr = new int[] { -1, 0, 1, 0 };
  int[] dc = new int[] { 0, -1, 0, 1 };
  int[][] grid;
  boolean[][] chk;
  int[] father;
  int m;
  int n;

  public int maxAreaOfIsland(int[][] grid) {
    this.grid = grid;
    m = grid.length;
    n = grid[0].length;
    chk = new boolean[m][n];
    father = new int[m * n];
    Arrays.fill(father, -1);
    int result = 0;
    for (int i = 0; i < m; i++) {
      for (int j = 0; j < n; j++) {
        if (grid[i][j] == 0 || chk[i][j])
          continue;
        int myIndex = i * n + j;
        // 携带father下标传下去，dfs过程中，扫到的下标都会变成这个下标
        int res = dfs(i, j, myIndex);
        father[myIndex] = -1 * res;
        result = Math.max(result, res);
      }
    }
    return result;
  }

  public int dfs(int i, int j, int index) {
    if (i < 0 || i >= m || j < 0 || j >= n || chk[i][j] || grid[i][j] == 0)
      return 0;
    int res = 1;
    int myIndex = i * n + j;
    father[myIndex] = index;
    chk[i][j] = true;
    for (int k = 0; k < 4; k++) {
      res += dfs(i + dr[k], j + dc[k], index);
    }
    return res;
  }
```



## 二维查找

### [搜索二维矩阵 II](https://leetcode.cn/problems/search-a-2d-matrix-ii/)

```java
public boolean searchMatrix(int[][] matrix, int target) {
  int x = matrix[0].length - 1;
  int y = 0;
  while (y < matrix.length && x >= 0) {
    if (matrix[y][x] == target)
      return true;
    else if (matrix[y][x] > target)
      x--;
    else
      y++;
  }
  return false;
}
```

Z字形搜索，控制一个方向为增大，一个方向为减小，这样就不用考虑两个方向同为增大而进行分类讨论了。

也可以逐行进行二分查找。

## 双指针搜索
[三数之和](https://leetcode.cn/problems/3sum/)
```go
func threeSum(nums []int) [][]int {
	sort.Ints(nums)
	res := [][]int{}
	// 找出a + b + c = 0
	// a = nums[i], b = nums[left], c = nums[right]
	for i := 0; i < len(nums)-2; i++ {
		// 排序之后如果第一个元素已经大于零，那么无论如何组合都不可能凑成三元组，直接返回结果就可以了
		n1 := nums[i]
		if n1 > 0 {
			break
		}
		// 去重a
		if i > 0 && n1 == nums[i-1] {
			continue
		}
		l, r := i+1, len(nums)-1
		for l < r {
			n2, n3 := nums[l], nums[r]
			if n1+n2+n3 == 0 {
				res = append(res, []int{n1, n2, n3})
				// 去重逻辑应该放在找到一个三元组之后，对b 和 c去重
				for l < r && nums[l] == n2 {
					l++
				}
				for l < r && nums[r] == n3 {
					r--
				}
			} else if n1+n2+n3 < 0 {
				l++
			} else {
				r--
			}
		}
	}
	return res
}
```