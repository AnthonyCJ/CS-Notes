# LeetCode_2_两数相加

---

## 方法：迭代

### 题目分析

将两个链表视为长度一致进行遍历，对于短的链表，对应节点的值按0计算。迭代过程中每一个新节点的值可按如下步骤计算。

1. 取两个节点的值： int x = l1 == null ? 0 : l1.val; int y = l2 == null ? 0 : l2.val;
2. 计算和（包括来自上一步的进位） sum = x + y + carry;
3. 保存本次产生的进位 carry = sum / 10;
4. 计算当前位应存储的值（仅保留个位） sum = sum % 10;
5. 新建节点 currNode.next = new ListNode(sum, null);
6. 迭代 currNode = curr.next; if (l1 != null) l1 = l1.next; if (l2 != null) l2 = l2.next;

末尾细节处理：如果链表节点全部遍历完毕时仍存在进位，则要在最后补充一个值为进位 carry 的节点。

### 代码实现

```java
/**
 * Iteration
 * Time:O(n), Space:O(1)
 * Version 1.0 2021-07-06 XCJ committed
 * @param l1 head of list1
 * @param l2 head of list2
 * @return head node after addition
 */
public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
    // initialize head and tool pointer
    ListNode dummyHead = new ListNode(-1);
    ListNode currNode = dummyHead;

    int carry = 0, sum;
    // Iteration
    while (l1 != null || l2 != null) {
        int x = l1 == null ? 0 : l1.val;
        int y = l2 == null ? 0 : l2.val;
        sum = x + y + carry;
        carry = sum / 10;
        sum = sum % 10;
        currNode.next = new ListNode(sum, null);
        // Iteration
        currNode = currNode.next;
        if (l1 != null) l1 = l1.next;
        if (l2 != null) l2 = l2.next;
    }
    // carry left for top digit
    if (carry != 0) currNode.next = new ListNode(carry, null);

    return dummyHead.next;
}
```