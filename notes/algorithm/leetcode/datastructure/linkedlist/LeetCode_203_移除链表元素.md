# LeetCode_203_移除链表元素

---

## 方法1：迭代

### 思路

增加虚头节点dummyHead，迭代过程中只需判断函数体，最后return dummyHead.next即可。

### 代码实现

```java
/**
 * Iteration
 * @param head head of the list
 * @param val ListNode of val should be deleted
 * @return head
 */
public ListNode removeElements02(ListNode head, int val) {
    ListNode dummyHead = new ListNode(0, head);
    ListNode p = dummyHead;
    while (p.next != null) {
        if (p.next.val == val)
            p.next = p.next.next;
        else
            p = p.next;
    }
    return dummyHead.next;
}
```

---

## 方法2：递归

### 思路

对于给定的链表，首先对除了头节点 head 以外的节点进行删除操作，然后判断 head 节点的值是否等于给定的 val。如果 head.val == val，则 head 需要被删除，因此删除操作后头节点为 head.next；如果 head.val != val，则保留 head，删除操作后头节点仍然是 head。上述过程为递归过程。

* 递归出口：head == null。此时返回 head。
* 当 head 不为 null，递归地进行删除操作，然后判断 head.val 是否等于 val 并决定是否要删除 head。

### 代码实现

```java
/**
 * Recursion Solution
 * @param head head of the list
 * @param val ListNode of val should be deleted
 * @return head
 */
public ListNode removeElements(ListNode head, int val) {
    if (head == null) return head;
    // iteration
    head.next = removeElements(head.next, val);
    // handle head
    return head.val == val ? head.next : head;
}
```