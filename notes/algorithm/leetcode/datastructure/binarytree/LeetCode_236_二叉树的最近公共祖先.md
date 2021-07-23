# LeetCode_236_二叉树的最近公共祖先

难度（medium）

---

## 方法：递归

### 思路

该问题显然可以用递归来实现。由于要计算公共祖先，所以要先处理完子节点（判断子树是否含有目标节点）后才能得出根节点的结果，所以递归函数的格式应该是后序遍历的框架（先递归左子树，再递归右子树、再处理根节点并进行判断）。由题意，该递归方法应含有三个参数：根节点 root，两个目标节点 p, q。

* 递归出口：root == null
* 定义子问题：（左子树分别包含 p, q）或 （当前节点为 p 或 q，且有一颗子树包含 p 或 q）。设函数 f 表示子树包含目标节点，则子问题转换为表达式如下

~~~java
	(f lson && f rson) ||  ((root == p || root == q) && (f lson || f rson)
~~~

### 复杂度分析

* 时间复杂度：O(n)，二叉树的所有结点均被访问一次。

* 空间复杂度：O(n)，空间复杂度取决于二叉树的高度。最好情况 O(logn)，最坏情况为二叉树呈链状，此时二叉树高度为 n。

### 代码实现

```java
private TreeNode ancestorNode;  // 祖先节点

/**
 * 判断树 root 是否包含 p 或 q（深度优先搜索）
 * @param root 根节点
 * @param p 目标节点1
 * @param q 目标节点2
 * @return true: 子树 root 包含目标节点 false: 子树 root 不包含目标节点
 */
private boolean dfs(TreeNode root, TreeNode p, TreeNode q) {
    // 递归出口
    if (root == null) {
        return false;
    }
    // 递归调用
    boolean bLeftSon = dfs(root.left, p, q);
    boolean bRightSon = dfs(root.right, p, q);
    /* 后续遍历位 */
    if ((bLeftSon && bRightSon) || ((root == p || root == q) && (bLeftSon || bRightSon))) {
        ancestorNode = root;
    }
    return bLeftSon || bRightSon || (root.val == p.val || root.val == q.val);
}

public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
    this.dfs(root, p, q);
    return ancestorNode;
}
```