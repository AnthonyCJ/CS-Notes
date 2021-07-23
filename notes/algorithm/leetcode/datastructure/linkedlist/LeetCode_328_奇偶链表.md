# LeetCode_328_奇偶链表

---

## 方法：双指针

### 思路

迭代判断条件为 while (even != null && eevene.next != null)

分析迭代过程可行性。节点个数为偶数，将在 even 指向最后一个节点时停止迭代；节点个数为奇数，将在 odd 指向最后一个节点，even 指向 null 时停止迭代。迭代可行。

本题的目的是按奇偶**位置**分离一个链表，将奇位置的节点放在前，偶位置的节点放在后。所以总体思路为将原链表通过一次遍历分为奇、偶两个链表，遍历至表尾后将偶链表接至奇链表后。奇链表使用 head 指向即可，偶链表需要定义一个evenHead指向头节点，迭代过程需要even, odd 两个指针分别指向奇位置和偶位置节点。

### 迭代步骤

1. 奇节点连接至下一奇节点
2. 偶节点连接至下一偶节点
3. 更新奇指针
4. 更新偶指针

### 代码实现

```java
/**
 * Modified Version by XCJ 2021-06-30
 * @param head head of the list
 * @return new head of oddEvenList
 */
public ListNode oddEvenList02(ListNode head) {
    if (head == null) return head;

    ListNode odd = head, even = head.next, evenHead = even;
    while (even != null && even.next != null) {
        odd.next = odd.next.next;
        even.next = even.next.next;
        odd = odd.next;
        even = even.next;
    }
    // link the EvenList to the OddList
    odd.next = evenHead;
    return head;
}
```