---
title: "链表"
description: 
date: 2022-08-15T10:46:49+08:00
image: 
math: 
license: 
hidden: false
comments: true
draft: false
categories:
  - 刷题
---
# 链表

## 翻转链表

### [ K 个一组翻转链表](https://leetcode.cn/problems/reverse-nodes-in-k-group/)

![](../../assets/img/截屏2022-08-15.png)

```java
public ListNode reverseKGroup(ListNode head, int k) {
  if (k == 1 || head == null)
    return head;
  ListNode blank = new ListNode();
  blank.next = head;
  int count = k;
  ListNode last = head;
  while (count > 0) {
    last = last.next;
    --count;
    if (last == null && count > 0)
      return head;
  }
  ListNode prev = blank;
  ListNode now = head;
  ListNode next = now;
  while(now!=last){
    next = now.next;
    now.next = prev;
    prev = now;
    now = next;
  }
  head.next = reverseKGroup(now, k);
  return prev;
}
```

