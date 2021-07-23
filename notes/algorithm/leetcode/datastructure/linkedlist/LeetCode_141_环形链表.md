#  LeetCode_141_环形链表

难度（easy）

---

## 方法：迭代

### 目标

给定头节点判断链表是否有环

### 思路

使用快慢指针解决问题。如果链表有环，快指针会先进入环，慢指针会后进入环，两者进入环后必然会在某点追及。判断有环的方法就是找到某一时刻两指针指向的ListNode相同（此处可用==判断，即引用相同）。

* 迭代条件：如果快慢指针指向不同节点，则不能判断是否有环，指针移动。
* 返回条件：
  * **return false——找到null引用**。初始状态，如果head == null 或 head->next == null 则可判断链表不含环。接下来通过while()循环向下遍历，因为fast 跑在前面，所以每次迭代判断fast == null || fast -> next == null。如果为真返回fase。
  * **return true —— slow == fast** 为真返回 true

### 细节处理

快慢指针的初始位置如何设置？（快慢指针都从head节点开始还是慢指针从head开始、快指针从head->next开始？）

这取决于设计思路。因为不清楚具体循环次数，我们要通过while（）循环找到两指针指向同一节点的时刻。所以while()循环的循环条件应该为slow != fast。若要保证顺利进入循环体，则初始状态不能是slow == fast。所以slow = head, fast = head->next; 

附：如果使用do - while()循环，即可将快慢指针都初始化指向head节点。（此处的关键就是保证顺利进入循环体）。快慢指针第二题的数学推导建立在“快慢指针起始位置相同”条件之上，所以我采用do-while()循环重写了该方法。

### 代码实现

```java
/**
 * Solution of double pointers.
 * @param head first node of the list
 * @return true-has cycle, false-no cycle
 */
public boolean hasCycle(ListNode head) {
    // special conditions
    if (head == null || head.next == null) return false;

    ListNode slow = head, fast = head;
    do {
        if (fast == null || fast.next == null) return false;
        slow = slow.next;
        fast = fast.next.next;
    } while (slow != fast);
    return true;
}
```