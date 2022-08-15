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
