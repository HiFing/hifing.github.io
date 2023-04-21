---
title: "树"
description: 
date: 2022-07-04T12:10:40+08:00
image: 
math: 
license: 
hidden: false
comments: true
draft: false
categories:
  - 刷题
---

# 树

## [序列化与反序列化](https://leetcode.cn/problems/h54YBf/)

```java
  class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;

    TreeNode(int x) {
      val = x;
    }
  }

  class Codec {
    // Encodes a tree to a single string.
    public String serialize(TreeNode root) {
      Queue<TreeNode> queue = new LinkedList<>();
      StringBuilder sb = new StringBuilder();
      queue.add(root);
      while (!queue.isEmpty()) {
        TreeNode tn = queue.remove();
        if (tn == null) {
          sb.append("*,");
        } else {
          sb.append(tn.val + ",");
          queue.add(tn.left);
          queue.add(tn.right);
        }
      }
      System.out.println(sb.toString());
      return sb.toString();
    }

    // Decodes your encoded data to tree.
    public TreeNode deserialize(String data) {
      if (data.length() == 0)
        return null;
      String[] strs = data.split(",");
      int n = strs.length;
      if (strs[0].equals("*"))
        return null;
      TreeNode head = new TreeNode(Integer.valueOf(strs[0]));
      Queue<TreeNode> queue = new LinkedList<>();
      queue.add(head);
      int index = 1;
      while (!queue.isEmpty() && index < n) {
        TreeNode tn = queue.remove();
        TreeNode left = (strs[index].equals("*") ? null : new TreeNode(Integer.valueOf(strs[index])));
        index++;
        TreeNode right = (strs[index].equals("*") ? null : new TreeNode(Integer.valueOf(strs[index])));
        index++;
        tn.left = left;
        tn.right = right;
        if (left != null)
          queue.add(left);
        if (right != null)
          queue.add(right);
      }
      return head;
    }
  }

```

## 树的搜索

### LCA：[二叉树的最近公共祖先](https://leetcode.cn/problems/lowest-common-ancestor-of-a-binary-tree/)

```java
class Solution {
  public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
    if (root == null)
      return root;
    if (root == p || root == q)
      return root;
    TreeNode left = lowestCommonAncestor(root.left, p, q);
    TreeNode right = lowestCommonAncestor(root.right, p, q);
    if (left != null && right != null)
      return root;
    if (left != null)
      return left;
    if (right != null)
      return right;
    return null;
  }
}
```

![](../../assets/img/Snipaste_2022-08-15_23-55-43.png)

### [二叉树中的最大路径和](https://leetcode.cn/problems/binary-tree-maximum-path-sum/)

```java
final int minimum = -1000;
int res = minimum;

public int search(TreeNode root) {
  if (root == null)
    return minimum;
  int left = search(root.left);
  int right = search(root.right);
  int maximumCombine = Math.max(0, Math.max(left, right)) + root.val;
  res = Math.max(res, Math.max(maximumCombine, left + right + root.val));
  return maximumCombine;
}

public int maxPathSum(TreeNode root) {
  search(root);
  return res;
}
```

更新res的时候有多种情况。返回上层时要确保root在计算结果中，以便上层能够连接子树结果。

### [验证二叉搜索树](https://leetcode.cn/problems/validate-binary-search-tree/)

```java
class Solution {
  public boolean chk(TreeNode root, long min, long max) {
    if (root == null)
      return true;
    if (root.val >= max || root.val <= min)
      return false;
    return chk(root.left, min, root.val) && chk(root.right, root.val, max);
  }

  public boolean isValidBST(TreeNode root) {
    if (root == null)
      return true;
    return chk(root, Long.MIN_VALUE, Long.MAX_VALUE);
  }
}
```

递归的时候把上下界传下去。

### [二叉树最大宽度](https://leetcode.cn/problems/maximum-width-of-binary-tree/)

DFS思路：按照先序，记录每层次最左侧的节点的满二叉树中的下标到Map中，若是已经有了，则减去已有的作为一个tmpResult。

BFS思路：按照层次遍历，入队时记录下满二叉树对应的下标，每一层最后一个元素和第一个元素下标做差。

