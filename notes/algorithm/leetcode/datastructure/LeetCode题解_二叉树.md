# LeetCode题解_二叉树

---

## 二叉树递归心得

* 写递归算法的关键是明确递归函数的【定义】是什么，然后利用定义推导最终结果。尽量不要跳入递归的细节（不要用大脑进行压栈）。
* 写树相关的算法，一个比较实用的思路是，先弄清楚 **null** 节点如何处理， **root** 节点该做什么，然后根据函数定义递归地处理子节点。递归调用会让子节点做相同的事情。

## 二叉树题目

* [1.LeetCode_144_二叉树的前序遍历](https://github.com/AnthonyCJ/CS-Notes/blob/trunk/notes/algorithm/leetcode/datastructure/binarytree/LeetCode_144_二叉树的前序遍历.md)

> 递归
>
> Morris 遍历法

* [2.LeetCode_101_对称二叉树](https://github.com/AnthonyCJ/CS-Notes/blob/trunk/notes/algorithm/leetcode/datastructure/binarytree/LeetCode_101_对称二叉树.md)

>递归
>
>迭代

* [3.LeetCode_112_路径总和](https://github.com/AnthonyCJ/CS-Notes/blob/trunk/notes/algorithm/leetcode/datastructure/binarytree/LeetCode_112_路径总和.md)

> 深度优先搜索

* [4.LeetCode_106_从中序遍历与后序遍历序列构造二叉树](https://github.com/AnthonyCJ/CS-Notes/blob/trunk/notes/algorithm/leetcode/datastructure/binarytree/LeetCode_106_从中序遍历与后序遍历序列构造二叉树.md)

> 递归

* [5.LeetCode_226_翻转二叉树](https://github.com/AnthonyCJ/CS-Notes/blob/trunk/notes/algorithm/leetcode/datastructure/binarytree/LeetCode_226_翻转二叉树.md)

  > 递归

* [6.LeetCode_116_填充每个节点的下一个右侧指针](https://github.com/AnthonyCJ/CS-Notes/blob/trunk/notes/algorithm/leetcode/datastructure/binarytree/LeetCode_116_填充每个节点的下一个右侧指针.md)

> 递归
>
> 迭代（使用自己建立的 next 指针）
>

* [7.LeetCode_117_填充每个节点的下一个右侧指针(2)](https://github.com/AnthonyCJ/CS-Notes/blob/trunk/notes/algorithm/leetcode/datastructure/binarytree/LeetCode_117_填充每个节点的下一个右侧指针(2).md)

> 迭代（使用自己建立的 next 指针）

* [8.LeetCode_236_二叉树的最近公共祖先](https://github.com/AnthonyCJ/CS-Notes/blob/trunk/notes/algorithm/leetcode/datastructure/binarytree/LeetCode_236_二叉树的最近公共祖先.md)

> 递归

* [9.LeetCode_297_二叉树的序列化与反序列化](https://github.com/AnthonyCJ/CS-Notes/blob/trunk/notes/algorithm/leetcode/datastructure/binarytree/LeetCode_297_二叉树的序列化与反序列化.md)

> 前序遍历递归算法 （涉及知识点**字符串处理** + **自动装箱**）

* [10.LeetCode_114_二叉树展开为链表](https://github.com/AnthonyCJ/CS-Notes/blob/trunk/notes/algorithm/leetcode/datastructure/binarytree/LeetCode_114_二叉树展开为链表.md)

> 递归
>
> 迭代

* [11.LeetCode_654_最大二叉树](https://github.com/AnthonyCJ/CS-Notes/blob/trunk/notes/algorithm/leetcode/datastructure/binarytree/LeetCode_654_最大二叉树.md)

> 递归
