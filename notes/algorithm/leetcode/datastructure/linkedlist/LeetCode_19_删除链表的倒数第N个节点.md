

# LeetCode_19_删除链表的倒数第N个节点

---

## 方法：双指针

### 思路

使用快慢指针，让快指针先走n步，然后快慢指针同速前进。当快指针指向最后一个ListNode时（p2.next == null），慢指针刚好指向待删除节点的前驱节点。

此处有两个细节要**注意**：

* 同速前进时表尾指针是指向最后一个节点停止还是指向null时停止。

  此处的选择决定了两个指针同时向后移动时的终止条件，即while (p2 == null) 还是 while (p2.next == null)。如果选择p2 == null，则p2会指向LastNode.next；若想让p1指向倒数第n 个节点的前驱节点，需要让p1与p2间距为n+1；如果选择p2.next == null，则让让p1与p2间距为n即可。

* 表头指针需指向待删除节点的**前驱节点**，否则无法直接删除。

  此处又有一个细节，即如果待删除节点为头节点，则没有其前驱节点，无法使用通用算法删除该节点，所以这种特殊情况单独处理。在控制指针间距时，如果p2指向null，即出现了要删除头节点的情况，此时直接返回head.next即可。

### 步骤

1. 让快指针先走N步
2. 快慢指针同步移动至表尾
3. delete slow.next

### 代码实现

```java
public ListNode removeNthFromEnd(ListNode head, int n) {
    ListNode p1 = head, p2 = head;
    // p1 points at the predecessor of the node to be deleted
    for (int i = 0; i < n; i++) {
        p2 = p2.next;
        if (p2 == null) return head.next;
    }
    // find the tail
    while (p2.next != null) {
        p1 = p1.next;
        p2 = p2.next;
    }
    // delete p1
    p1.next = p1.next.next;

    return head;
}
```