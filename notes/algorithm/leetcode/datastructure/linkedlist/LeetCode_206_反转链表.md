# LeetCode_206_反转链表

---

## 方法1：迭代

### 思路

头插法。依次将原头节点的下一节点头插法移至链表头。需要两个指针，一个指向原头节点（oldHead），一个指向头节点下一个节点（负责移动该节点）。

### 注意

* 每次移动节点后要更新head指针，使其指向最新的头节点。
* 移动节点后指针需复位，继续指向原头节点的下一个节点。

### 迭代步骤

1. 取出一个节点
2. 将其连接至链表头
3. 更新head指针使其指向新的头
4. 复位

### 复杂度分析

* 时间复杂度：O(n)
* 空间复杂度：O(1)

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
```