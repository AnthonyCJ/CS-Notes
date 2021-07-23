# LeetCode_92_反转链表（反转指定区间）

---

## 方法1：递归

### 分析

首先实现反转前N个链表的递归解法。然后通过递归调用 left - 1 次移动至递归出口 left == 1，此时相当于反转以 head 为头节点的链表，直接调用<反转前N个链表的递归方法>即可。反转前N个链表的递归解法与反转全部节点的不同之处在于：

1. 递归出口变为 n == 1, 同时要记录后继节点 successor
2.  反转后要将 head 与后驱节点 successor 连接

### 步骤

1. 递归出口 if (left == 1) return reverseN( head, n);
2. 递归迭代语句（前进至反转起点触发递归出口） head.next = reverseBetween(head.next, left - 1, right - 1); 
3. 返回头节点

### 代码实现

```java
ListNode successor = null;  // 后继节点
// 反转以 head 为起点的前 n 个节点，返回新的头节点
public ListNode reverseN(ListNode head, int n) {
    // 递归出口
    if (n == 1) {
        // 记录第 n + 1 个节点
        successor = head.next;
        return head;
    }
    // 递归方程，处理剩余部分
    ListNode newHead = reverseN(head.next, n - 1);
    // 将已反转的剩余部分指向 head 节点
    head.next.next = head;
    // 连接反转部分和未反转部分
    head.next = successor;
    return newHead;
}
/**
 * Recursion
 * Time:O(n), Space:O(n)
 * Version 1.0 2021-07-13 by XCJ
 * @param head head of the list
 * @param left start reversion at index of left
 * @param right end reversion at index of right
 * @return head of the list after reversion
 */
public ListNode reverseBetween_Recursion(ListNode head, int left, int right) {
    // 递归出口
    if (left == 1) {
        // 相当于反转以 head 为头节点的前 n 个元素
        return reverseN(head, right);
    }
    // 如果不符合条件，则递归前进至递归出口
    head.next = reverseBetween_Recursion(head.next, left - 1, right - 1);
    return head;
}
```

---

## 方法2：迭代

### 分析

原头节点可能改变，所以设置虚拟头节点。反转中间部分链表后要与前后连接，所以要找到起始位置的前驱节点。剩余反转部分通过 for 循环控制反转节点数量。

### 步骤

1. 设置虚拟头节点
2. 找到起始节点的前驱 
3. for 循环迭代 right - left 次进行反转操作
4. 返回 dummyHead.next

### 代码实现

```java
/**
 * Iteration
 * Time:O(n), Space:O(1)
 * Version 1.0 2021-07-13 by XCJ
 * @param head head of the list
 * @param left start reversion at index of left
 * @param right end reversion at index of right
 * @return head of the list after reversion
 */
public ListNode reverseBetween_Iteration(ListNode head, int left, int right) {
    if (head == null || head.next == null) return head;

    ListNode dummyHead = new ListNode(-1); dummyHead.next = head;
    // find the precursor of the node to be reversed
    ListNode preNode = dummyHead;
    for (int i = 0; i < left - 1; i++) {
        preNode = preNode.next;
    }
    ListNode oldHead = preNode.next;
    ListNode nextNode = oldHead.next;
    for (int i = 0; i < right - left; i++) {
        // cut
        oldHead.next = nextNode.next;
        // link
        nextNode.next = preNode.next;
        // update head
        preNode.next = nextNode;
        // update nextNode
        nextNode = oldHead.next;
    }

    return dummyHead.next;
}
```