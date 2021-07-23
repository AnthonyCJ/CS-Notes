# LeetCode_112_路径总和

难度（easy）

---

## 方法1：递归（深度优先搜索）

### 思路

利用递归探测可行路线，同时缩减问题规模。

* 递归出口：root == null 及 访问至叶子节点（此时直接判断 root.val 是否等于 targetSum。
* 递归调用：假设当前节点值为 value，是否存在一条从当前节点到某叶子节点的路径，和为 targetSum - value。所以递归调用时，递归参数为 targetSum - this.value。

### 复杂度分析

* 时间复杂度：最坏时间复杂度为O(n)。
* 空间复杂度：最好情况是满二叉树，复杂度为O(logn)，最坏情况是二叉树呈链状，复杂度为O(n)。

> 注：可尝试优化内存。

### 代码实现

```java
/**
 * Recursion
 * Time:O(n), Space:O(height)
 * Version 1.0 2021-07-16 by XCJ
 * @param root root of the tree
 * @param targetSum sum of the values in the path
 * @return true, the path exists | false, the path doesn't exist
 */
public boolean hasPathSum(TreeNode root, int targetSum) {
    if (root == null) {
        return false;
    }
    if (root.left == null && root.right == null) {
        return targetSum == root.val;
    }
    return hasPathSum(root.left, targetSum - root.val) ||
            hasPathSum(root.right, targetSum - root.val);
}
```

---

## 优化：递归（深度优先搜索）

### 优化思路

从运行过程尝试对方法1进行优化。观察方法1的递归迭代语句可以发现，无论 root.left 是否为空，都会递归地调用 hasPathSum(root.left, ratgetSum - root.val)，对 root.left 的判断在方法内进行，如果 root.left == null，再退出。此时系统经历了函数调用，访问 root 指针，函数返回的操作。相比较而言，如果将对 root.left == null 的判断放在函数调用前，针对 root.left == null 的情况，系统只需访问 root.left，并作一次判断。

### 注意

由于将对 root 节点是否为 null 的判断移至函数调用外，所以要在每次函数调用前判断 root 是否为 null，否则将产生 NullPoinerException。

### 复杂度分析

* 与方法1一致。

### 代码实现

```java
/**
 * Recursion_Modified
 * Time:O(n), Space:O(height)
 * Version 2.0 2021-07-16 by XCJ
 * @param root root of the tree
 * @param targetSum sum of the values in the path
 * @return true, the path exists | false, the path doesn't exist
 */
public boolean hasPathSum_DFS(TreeNode root, int targetSum) {
    if (root == null) {
        return false;
    }
    return dfs(root, targetSum);
}

private boolean dfs(TreeNode root, int num) {
    // leaf node
    if (root.left == null && root.right == null) {
        return (num - root.val) == 0;
    }
    // has left child || right child
    return (root.left != null && dfs(root.left,num - root.val)) ||
            (root.right != null && dfs(root.right, num - root.val));
}
```

