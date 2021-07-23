# LeetCode_160_相交链表

---

## 方法：双指针

### 思路

从数学的角度优化思想，减少分支判断条件。

设 A 的长度为 a + c，B 的长度为 b + c，其中 c 为尾部公共部分长度，可知 a + c + b = b + c + a。

当访问链表 A 的指针访问到链表尾部时，令它从链表 B 的头部重新开始访问链表 B；同样地，当访问链表 B 的指针访问到链表尾部时，令它从链表 A 的头部重新开始访问链表 A。这样就能控制访问 A 和 B 两个链表的指针能同时访问到交点。

### 代码实现

```java
/**
 * Modified version.
 * @Version 2.0
 * @param headA head of list A
 * @param headB head of list B
 * @return ListNode-point of crossing | null-no crossing point
 */
public ListNode getIntersectionNode02(ListNode headA, ListNode headB) {
    // invalid situations
    if (headA == null || headB == null) return null;

    ListNode pA = headA, pB = headB;
    while (pA != pB) {
        pA = pA == null ? headB : pA.next;
        pB = pB == null ? headA : pB.next;
    }
    return pA;
}
```