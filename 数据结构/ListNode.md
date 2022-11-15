---
title: ListNode 链表
---

#### 介绍

链表是一种物理存储单元上非连续、非顺序的存储结构。因为它是通过 Map（JavaScript）来实现的，它所拥有的逻辑顺序是通过指针连接次序实现的。

由一系列的节点组成，没个节点包含两个部分，一个存储数据，一个存储下一个节点的地址

相比数组，链表在新增和删除节点上更胜一筹，但是在迭代上却只能从头开始访问，需要的时间为 O(n)

优点
克服了数组需要预先知道数据大小的缺点，实现灵活的内存动态管理

缺点
失去了数组随机读取的优点，无法通过下标直接访问某个元素

```ts
export class ListNode<T = any> {
  val: T;
  next: typeof ListNode | undefined;
  constructor(val?: any, next?: typeof ListNode) {
    this.val = val;
    this.next = next;
  }
}
```

#### 双向链表

是在基础的链表上增加一个 prev 属性来指向上一个节点

```ts
export class doubleListNode<T = any> {
  val: T;
  next: typeof doubleListNode | undefined;
  prev: typeof doubleListNode | undefined;
  constructor(
    val?: any,
    next?: typeof doubleListNode,
    prev?: typeof doubleListNode
  ) {
    this.val = val;
    this.next = next;
    this.prev = this.prev;
  }
}
```
