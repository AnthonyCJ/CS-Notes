# LeetCode_234_回文链表

---

## 方法：双指针

### 思路

使用快慢指针找到链表中点，然后将后半段链表逆序，再以同速正向遍历一次两个链表，若每个节点值都相同，则是回文链表。

### 步骤

1. 快慢指针找中点
2. 逆序
3. 双指针遍历

### 代码实现

```java
/**
 * 迭代法反转链表
 * @param head old head of the list
 * @return new head after reversion
 */
public ListNode reverseList(ListNode head) {
    if (head == null) return null;
    // oldHead指向原head，q指向原oldHead.next
    ListNode oldHead = head, nextNode = oldHead.next;
    while (oldHead.next != null) {
        // 取出
        oldHead.next = nextNode.next;
        // 连接
        nextNode.next = head;
        // 更新head
        head = nextNode;
        // 复位
        nextNode = oldHead.next;
    }
    return head;
}

/**
 * double pointer to divide the list into two lists
 * Version 1.0 2021-07-03 XCJ committed
 * @param head head of the list
 * @return true it is a palindrome list | false it is not a palindrome list
 */
public boolean isPalindrome(ListNode head) {
    if (head == null || head.next == null) return true;

    ListNode slowPtr = head, fastPtr = head.next;
    // find the middle node of the list
    while (fastPtr != null && fastPtr.next != null) {
        fastPtr = fastPtr.next.next;
        slowPtr = slowPtr.next;
    }
    // reverse the latter-half list
    fastPtr = reverseList(slowPtr.next);
    slowPtr = head;
    // checkout palindrome
    while (slowPtr != null && fastPtr != null) {
        if (slowPtr.val != fastPtr.val) return false;
        slowPtr = slowPtr.next;
        fastPtr = fastPtr.next;
    }
    return true;
}
```