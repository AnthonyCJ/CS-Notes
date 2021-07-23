# LeetCode_226_翻转二叉树

难度（easy）

---

## 思路

本题要考虑如下几个方面。递归出口、对根节点的操作、操作位置，递归地处理子节点。

* 递归出口：root == null

* 对根节点的操作：交换根节点的两个子树

* 操作位置：前序遍历或后序遍历

*  递归地处理子节点：

  ​	invert(root.left); 

  ​	invert(root.right);

## 代码实现

```java
/**
 * Recursion
 * Version 1.0 2021-07-17 by XCJ
 * @param root root of the tree to be inverted
 * @return root of the tree after inversion
 */
public TreeNode invertTree(TreeNode root) {
    // 递归出口
    if (root == null) {
        return null;
    }
    // 交换子树
    TreeNode tempNode = root.left;
    root.left = root.right;
    root.right = tempNode;
    // 递归处理子树
    invertTree(root.left);
    invertTree(root.right);

    return root;
}
```