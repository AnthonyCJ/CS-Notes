# LeetCode_25_K个一组翻转链表

---

## 方法：迭代（虚拟头节点 + 双指针）

### 分析

用迭代法处理本题有**两个难点**，一个是如何翻转小组内的 K 个节点，另一个是如何将翻转的 K 个小组连接起来。

* 对于第一个难点，可构造包含以（翻转起始节点 a、翻转终止节点 b）为参数的翻转链表方法，将翻转后的链表头节点返回。该方法的迭代终止条件由原来的 oldHead.next != null 改为 oldHead.next != b 即可。
* 对于第二个难点，可以使用虚拟头节点。本题需要两个虚拟头节点，dummyHead, tempDummyHead。dummyHead 是全链表的虚拟头节点，作为返回值返回，tempDummyHead 是小组的虚拟头节点，负责连接每一个翻转后的小组。每翻转一组，tempDummyHead 向后迭代 K 个位置。

迭代终止条件为末尾指针指向 null，此时返回 dummyHead 即可。

### 步骤

1. 单独处理第一组，保存虚拟头节点 dummyHead。
2. 循环处理剩余组。内部顺序为
   1.  找到下一组前驱节点
   2. 更新边界 tempHead, tempTail
   3. 翻转小组元素
3. 终止条件：找到元素不足 K 的小组终止迭代

### 注意

* 作为**实参**的两个变量 tempHead, tempTail，其**迭代方法**会影响结果的正误。Java 方法中的形参是实参的一个拷贝，方法返回后形参会被释放。如果实参为引用类型，且方法调用过程中改变了形参（其拷贝）的值，则函数调用返回后，虽然形参被释放，实参指向的对象（内存单元内容）依然会被改变。

*  对于本题，我使用双指针 tempHead, tempTail 作为每一组链表翻转的边界。常规迭代思路是在调用 reverseList(tempHead, tempTail) 后，与临时虚拟头指针 tempDummyHead 同时向后迭代 K 个位置。在作为实参调用 reverseList(tempHead, tempTail) 后，tempHead，tempTail 已不再指向原位置的小组首元素和小组尾元素，同时，经反转后，next 指针所指内容也发生变化，所以如果直接在 tempHead, tempTail, 的基础上向后迭代，则无法到达预期位置。
* 我的解决办法是：使用临时虚拟头节点 tempDummyHead，在每一组翻转前指向其前驱节点，在反转后，按顺序向后迭代 K 个位置即可指向下一组的前驱节点。此时以 tempDummyHead 为基地址，向后偏移 1 个位置及 K 个位置即可分别得到正确的 tempHead, tempTail 值。

### 代码实现

```java
/**
 * 反转链表（从节点 a 开始，到节点 b 为止）
 * @param a 翻转起始节点（包括节点a）
 * @param b 反转终止节点（不包括节点b）
 * @return 翻转后头节点
 */
ListNode reverseList(ListNode a, ListNode b) {
    ListNode oldHead = a, newHead = a, nextNode = oldHead.next;
    while (oldHead.next != b) {
        // cut
        oldHead.next = nextNode.next;
        // link
        nextNode.next = newHead;
        // update newHead
        newHead = nextNode;
        // update nextNode
        nextNode = oldHead.next;
    }
    return newHead;
}

/**
 * K个一组反转链表
 * Iteration
 * Time:O(n), Space:O(1)
 * Version 1.0 2021-07-13 by XCJ
 * @param head 链表头节点
 * @param k 每组包含的元素个数
 * @return 反转后的新头节点
 */
public ListNode reverseKGroup(ListNode head, int k) {
    if (head == null || head.next == null) return head;

    ListNode dummyHead = new ListNode(-1, head);
    ListNode tempDummyHead = dummyHead, tempHead = head, tempTail = head;
    for (int i = 0; i < k; i++) {
        tempTail = tempTail.next;
    }
    // handle dummyHead (the first group)
    dummyHead.next = reverseList(tempHead, tempTail);
    tempDummyHead.next = dummyHead.next;
    // the rest listNodes
    while (true) {
        // find the precursor of the next group
        for (int i = 0; i < k; i++) {
            tempDummyHead = tempDummyHead.next;
        }
        // update tempHead and tempTail
        tempHead = tempDummyHead.next;
        tempTail = tempHead;
        for (int i = 0; i < k; i++) {
            if (tempTail == null)
                return dummyHead.next;
            else
                tempTail = tempTail.next;
        }
        tempDummyHead.next = reverseList(tempHead, tempTail);
    }
}
```