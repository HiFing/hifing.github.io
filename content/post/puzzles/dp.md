---
title: 动态规划
description: 
date: 2022-06-12T21:20:31+08:00
draft: false
categories:
  - 刷题
---

# 动态规划

## 回文字串

### [回文子字符串的个数](https://leetcode.cn/problems/a7VOhD/)

```java
  // dp[i][j]表示形如“......，A[i]，A[j]”的序列
  public int lenLongestFibSubseq(int[] arr) {
    int n = arr.length;
    Map<Integer, Integer> map = new HashMap<>();
    for (int i = 0; i < n; i++) {
      map.putIfAbsent(arr[i], i);
    }
    int res = 0;
    Map<Integer, Integer> longest = new HashMap<>();
    for (int i = 0; i < n; i++) {
      for (int j = 0; j < i; j++) {
        int index = map.getOrDefault(arr[i] - arr[j], -1);
        if (index >= 0 && index < j) {
          int length = longest.getOrDefault(index * n + j, 2) + 1;
          longest.put(j * n + i, length);
          res = Math.max(res, length);
        }
      }
    }
    return res;
  }

  public int countSubstrings(String s) {
    int n = s.length();
    char[] str = s.toCharArray();
    int[][] dp = new int[n][n];
    int res = n;
    for (int i = 0; i < n; i++) {
      dp[i][i] = 1;
    }
    for (int i = 0; i < n; i++) {
      for (int j = i - 1; j >= 0; j--) {
        if (str[i] == str[j]) {
          if (j == i - 1) {
            dp[j][i] = 1;
            res++;
          } else if (dp[j + 1][i - 1] == 1) {
            dp[j][i] = 1;
            res++;
          }
        }
      }
    }
    return res;
  }
```

关联题：[最长回文子串](https://leetcode.cn/problems/longest-palindromic-substring/) （也可使用中心扩展的方法获取，要分奇偶两种情况）

> ```java
> class Solution {
>   char[] seq;
> 
>   private int findLength(int start, boolean flag) {
>     int left = start - (flag ? 1 : 0);
>     int right = start;
>     int length = (left == right ? -1 : 0);
>     while (left >= 0 && right < seq.length) {
>       if (seq[left] == seq[right]) {
>         length += 2;
>         left--;
>         right++;
>       } else
>         break;
>     }
>     return length;
>   }
> 
>   public String longestPalindrome(String s) {
>     seq = s.toCharArray();
>     int nowMax = 0;
>     int index = 0;
>     for (int i = 0; i < seq.length; i++) {
>       int singled = findLength(i, true);
>       int doubled = findLength(i, false);
>       if (singled > nowMax) {
>         index = i;
>         nowMax = singled;
>       }
>       if (doubled > nowMax) {
>         index = i;
>         nowMax = doubled;
>       }
>     }
>     return s.substring(index - (nowMax / 2), index - (nowMax / 2) + nowMax);
>   }
> }
> ```

## 矩阵

### [矩阵中的距离](https://leetcode.cn/problems/2bCMpM/)

![](../../assets/img/1626667205-xFxIeK-image.png)

```
输入：mat = [[0,0,0],[0,1,0],[1,1,1]]
输出：[[0,0,0],[0,1,0],[1,2,1]]
```

```java
class Solution {
  public int[][] updateMatrix(int[][] mat) {
    int m = mat.length;
    int n = mat[0].length;
    int dp[][] = new int[m][n];
    for (int i = 0; i < m; i++) {
      for (int j = 0; j < n; j++) {
        if (mat[i][j] == 0)
          continue;
        if (i == 0 && j == 0 && mat[0][0] != 0) {
          dp[0][0] = m + n;
          continue;
        }
        int up = (i == 0 ? m + n : dp[i - 1][j]);
        int left = (j == 0 ? m + n : dp[i][j - 1]);
        dp[i][j] = Math.min(up, left) + 1;
      }
    }
    for (int i = m - 1; i >= 0; i--) {
      for (int j = n - 1; j >= 0; j--) {
        if (i == m - 1 && j == n - 1)
          continue;
        int down = (i == m - 1 ? m + n : dp[i + 1][j]);
        int right = (j == n - 1 ? m + n : dp[i][j + 1]);
        dp[i][j] = Math.min(dp[i][j], Math.min(down, right) + 1);
      }
    }
    return dp;
  }
}
```

## 背包

> 背包问题总结：[『 一文搞懂完全背包问题 』从0-1背包到完全背包，逐层深入+数学推导 - 最少的硬币数目 - 力扣（LeetCode）](https://leetcode.cn/problems/gaM7Ch/solution/by-flix-7gza/)

### [最少的硬币数目](https://leetcode.cn/problems/gaM7Ch/)(完全背包数个数)

```
给定不同面额的硬币 coins 和一个总金额 amount。编写一个函数来计算可以凑成总金额所需的最少的硬币个数。如果没有任何一种硬币组合能组成总金额，返回 -1。

你可以认为每种硬币的数量是无限的。
```

```java
class Solution {
  public int coinChange(int[] coins, int amount) {
    int[] dp = new int[amount + 1];
    Arrays.fill(dp, Integer.MAX_VALUE);
    dp[0] = 0;
    for (int k = 0; k < coins.length; k++) {
      for (int i = 1; i <= amount; i++) {
        if (i - coins[k] < 0 || dp[i - coins[k]] == Integer.MAX_VALUE)
          continue;
        dp[i] = Math.min(dp[i], dp[i - coins[k]] + 1);
      }
    }
    return dp[amount] == Integer.MAX_VALUE ? -1 : dp[amount];
  }
}


  public int coinChange(int[] coins, int amount) {
    int[] dp = new int[amount + 1];
    Arrays.fill(dp, Integer.MAX_VALUE);
    dp[0] = 0;
    for (int i = 1; i <= amount; i++) {
      for (int k = 0; k < coins.length; k++) {
        if (i - coins[k] < 0 || dp[i - coins[k]] == Integer.MAX_VALUE)
          continue;
        dp[i] = Math.min(dp[i], dp[i - coins[k]] + 1);
      }
    }
    return dp[amount] == Integer.MAX_VALUE ? -1 : dp[amount];
  }
```

