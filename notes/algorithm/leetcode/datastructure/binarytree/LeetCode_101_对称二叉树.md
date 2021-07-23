# LeetCode_101_对称二叉树

难度（easy）

---

## 方法1：递归

### 思路

二叉树对称要满足以下条件

1. 两个子节点的值相同
2. 两个子节点的左右子节点对称

由条件我们需要构造一个以两个节点为参数的递归方法。

* 递归出口：节点值均为空（此时判定为对称）或仅有一个为空（此时判定不对称）。
* 递归调用：向下探测子节点的左右子指针是否对称。

### 复杂度分析

* 时间复杂度：O(n)。遍历全部节点。
* 空间复杂度：最好情况是满二叉树，复杂度为O(logn)，最坏情况是二叉树呈链状，复杂度为O(n)。

### 代码实现

```java
/**
 * Recursion
 * Time:O(n), Space:O(height)
 * Version 1.0 2021-07-16 by XCJ
 * @param root root of the binary tree
 * @return true, the tree is symmetric | false, the tree is not symmetric
 */
public boolean isSymmetric_Recursion(TreeNode root) {
    return check_Recursion(root, root);
}
public boolean check_Recursion(TreeNode p, TreeNode q) {
    if (p == null && q == null) {
        return true;
    }
    if (p == null || q == null) {
        return false;
    }
    return p.val == q.val && check_Recursion(p.left, q.right) && check_Recursion(p.right, q.left);
}
```

---

## 方法2：迭代

### 思路

依然是层序遍历的思想，所以由递归改写为迭代时采用队列实现。初始化时将根节点入队两次。每次让两个节点出队并比较其值（队列中每两个连续的节点的 value 应该相等，且它们的子树互为镜像），然后将两个结点的左右子结点按相反顺序入队。

* 终止条件：队空或检测到树不对称（即从队列中取出两个不相等的连续节点）。

### 复杂度分析

* 时间复杂度：O(n)。遍历全部节点。
* 空间复杂度：用一个队列维护节点，每个节点最多进队一次，出队一次，队列中最多不会超过 n 个点，故渐进空间复杂度为 O(n)。

### 代码实现

```java
/**
 * Iteration
 * Time:O(n), Space:O(n)
 * Version 1.0 2021-07-16 by XCJ
 * @param root root of the binary tree
 * @return true,  the tree is symmetric 
 */
public boolean isSymmetric_Iteration(TreeNode root) {
    return check_Iteration(root, root);
}
public boolean check_Iteration(TreeNode u, TreeNode v) {
    Queue<TreeNode> queue = new LinkedList<TreeNode>();
    queue.offer(u);
    queue.offer(v);
    while (!queue.isEmpty()) {
        u = queue.poll();
        v = queue.poll();
        if (u == null && v == null) {
            continue;
        }
        if ((u == null || v == null) || (u.val != v.val)) {
            return false;
        }

        queue.offer(u.left);
        queue.offer(v.right);

        queue.offer(u.right);
        queue.offer(v.left);
    }
    return true;
}
```