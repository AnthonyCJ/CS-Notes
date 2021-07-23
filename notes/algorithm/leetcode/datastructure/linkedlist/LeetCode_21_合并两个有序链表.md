# LeetCode_21_合并两个有序链表

---

## 方法1：递归

### 分析

由于要求合并为有序，而两个待合并链表的首元素大小并不清楚。若要以某个原链表头节点作为 newHead ，需要比较大小，增加代码量。为简化解题步骤，使用虚拟头节点 dummyHead ，将 dummyHead.next 作为返回值。

### 解题步骤

1. 创建虚拟头节点 dummyHead ，工具指针 preNode 指向待插入节点的前驱节点，初始化为指向 dummyHead。
2. 使用两个链表的 head 指针作为工具指针，从两个链表的头节点开始向后迭代。每次选取两个指针中较小的节点插入新链表。
3. preNode 指针和 对应链表头指针向后移动。
4. 重复（2）（3）至某一链表全部插入完毕 while (l1 != null && l2 != null)
5. 将非空链表连接至新链表表尾。 preHead.next = l1 == null ? l2 : l1;
6. return dummyHead.next;

### 代码实现

```java
/**
 * Iteration
 * Time:O(m+n), Space:O(1)
 * Version 1.0 2021-07-05 XCJ committed
 * @param l1 head of list1
 * @param l2 head of list2
 * @return head node after merge
 */
public ListNode mergeTwoLists01(ListNode l1, ListNode l2) {
    // 创建虚拟头节点，工作指针初始化指向头节点
    ListNode dummyHead = new ListNode(0);
    ListNode preNode = dummyHead;

    while (l1 != null && l2 != null) {
        if (l1.val < l2.val) {
            preNode.next = l1;
            l1 = l1.next;
        }
        else {
            preNode.next = l2;
            l2 = l2.next;
        }
        preNode = preNode.next;
    }
    // 将剩余链表直接连接至新链表表尾
    preNode.next = l1 == null ? l2 : l1;
    return dummyHead.next;
}
```

---

## 方法2：递归

### 代码实现

```java
/**
 * Recursion
 * Time:O(m+n), Space:O(n)
 * Version 1.0 2021-07-06 by LeetCode
 * @param l1 head of list1
 * @param l2 head of list2
 * @return head node after merge
 */
public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
    if (l1 == null) {
        return l2;
    }
    else if (l2 == null) {
        return l1;
    }
    else if (l1.val < l2.val) {
        l1.next =  mergeTwoLists(l1.next, l2);
        return l1;
    }
    else {
        l2.next =  mergeTwoLists(l2.next, l1);
        return l2;
    }
}
```