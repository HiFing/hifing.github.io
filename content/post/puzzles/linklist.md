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

### [重排链表](https://leetcode.cn/problems/reorder-list/)

```java
//使用Deque更方便，为追求极致的空间复杂度可以先找中点，再反转，再拼接
class Solution {
  public void reorderList(ListNode head) {
    Deque<ListNode> deque = new LinkedList<>();
    ListNode tmp = head.next;
    while (tmp != null) {
      deque.addLast(tmp);
      tmp = tmp.next;
    }
    boolean flag = true;
    tmp = head;
    while (!deque.isEmpty()) {
      tmp.next = (flag ? deque.pollLast() : deque.pollFirst());
      tmp = tmp.next;
      flag = !flag;
    }
    tmp.next=null;
  }
}
```

### [排序链表](https://leetcode.cn/problems/sort-list/)

```java
//可以对链表一分为二进行归并排序，在操作的过程中可以断开两个链表的链接，这样处理更加方便

//也可以参考快速排序，用两个空节点收录比pivot大的节点串和比pivot小的节点串，再对这两个串处理，处理之后按照“小->pivot->大”这样的顺序组装返回
```



## 环形链表找环的入口

### [环形链表 II](https://leetcode.cn/problems/linked-list-cycle-ii/)

```java
public class Solution {
  public ListNode detectCycle(ListNode head) {
    if(head == null) return null; 
    ListNode slow = head;
    ListNode fast = head.next;
    ListNode empty = new ListNode();
    empty.next = head;
    while (slow != fast && fast != null) {
      slow = slow.next;
      fast = fast.next;
      if (fast == null)
        break;
      fast = fast.next;
    }
    if (fast == null)
      return null;
    ListNode tmp = empty;
    while (tmp != fast) {
      tmp = tmp.next;
      fast = fast.next;
    }
    return tmp;
  }
}
```

![](../../assets/img/IMG_1E4C2917FDD4-1.jpeg)

## [LRU缓存机制](https://leetcode.cn/problems/lru-cache)

使用双向链表和哈希表的方式模拟LRU

```java
class Node {
  Node next;
  Node prev;
  int value;
  int key;
}

class LRUCache {
  int capacity;
  Node head;
  Node tail;
  Map<Integer, Node> map;

  public LRUCache(int capacity) {
    this.capacity = capacity;
    head = new Node();
    tail = new Node();
    head.next = tail;
    tail.prev = head;
    map = new HashMap<>();
  }

  // get附带更新最新节点功能
  public int get(int key) {
    if (map.containsKey(key)) {
      Node target = map.get(key);
      Node prev = target.prev;
      Node next = target.next;
      prev.next = next;
      next.prev = prev;
      Node headNext = head.next;
      head.next = target;
      target.prev = head;
      headNext.prev = target;
      target.next = headNext;
      return target.value;
    } else
      return -1;
  }

  public void put(int key, int value) {
    if (capacity <= 0)
      return;
    if (map.containsKey(key)) {
      Node n = map.get(key);
      n.value = value;
    } else {
      Node tmp = new Node();
      tmp.value = value;
      tmp.key = key;
      if (capacity == map.size()) {
        Node last = tail.prev;
        map.remove(last.key);
        last.prev.next = last.next;
        last.next.prev = last.prev;
      }
      Node last = tail.prev;
      last.next = tmp;
      tmp.prev = last;
      tmp.next = tail;
      tail.prev = tmp;
      map.put(key, tmp);
    }
    // 更新时调用一下get即可
    get(key);
  }
}
```

