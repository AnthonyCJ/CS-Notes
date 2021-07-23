# LeetCode_106_从中序遍历与后序遍历序列构造二叉树

难度（medium）

---

## 方法1：递归

### 思路

目标是构造二叉树，解决此问题首先明确如何对其进行中序遍历与后序遍历。

* 中序遍历的顺序是左子节点、根节点、右子节点
* 后序遍历的顺序是左子节点、右子节点、根节点

根据遍历顺序，后序遍历所得数组最后一个元素是根节点。由此特性，可以先在后序遍历数组中找到根节点，利用根节点信息在中序遍历数组中找到根节点所在下标，据此将中序遍历的数组分成左右两部分，左侧为左子树、右侧为右子树，针对每个部分用同样的方法递归地构造二叉树。

### 算法

1. 在后序遍历数组知道根节点元素信息后，为了在中序遍历数组找到根节点下标，有一个（由值查询下标）的操作。为提高查询效率，建立一个（元素值，下标）键值对的哈希表。
2. 定义递归函数，helper(in_left, in_right) 表示当前递归到中序序列中当前子树的左右边界，递归入口为 helper(0, n - 1) 
   1. 如果 in_left > in_right，说明子树为空，返回空节点。
   2.  选择后序遍历的最有一个节点作为根节点。
   3. 利用哈希表 O(1) 查询根节点在中序遍历中的下标 index。从 in_left 到 index - 1 属于左子树，从 index + 1 到 in_right 属于右子树。
   4. 根据后序遍历逻辑，递归创建右子树 helper(index + 1, in_right) 和左子树 helper(in_left, index - 1)。==注意==这里有需要**先创建右子树**，再创建左子树的依赖关系。可以理解为在后序遍历的数组中整个数组是先存储左子树的节点，再存储右子树的节点，最后存储根节点，如果按每次选择【后序遍历的后一个节点】为根节点，则先被构造出来的应该为右子树。
   5. 返回根节点 root。

### 复杂度分析

* 时间复杂度：O(n)，其中 n 是树中的节点个数。
* 空间复杂度：O(n)。需要使用O(n)的空间存储哈希表，以及O(height) （height 为树的高度）的空间分配递归栈空间。此处 height <= n，所以空间复杂度为 O(n)。

### 代码实现

```java
int postIndex;
int[] postorder;
int[] inorder;
Map<Integer, Integer> indexMap = new HashMap<Integer, Integer>();

public TreeNode builder(int inLeft, int inRight) {
    // 递归出口
    if (inLeft > inRight) return null;

    // 选择 postIndex 位置的元素作为当前子树根节点
    int rootVal = postorder[postIndex];
    TreeNode root = new TreeNode(rootVal);

    // 根据 root 所在位置分成左右两棵子树
    int index = indexMap.get(rootVal);

    postIndex--;    // 后序遍历数组根节点指针自减一
    root.right = builder(index + 1, inRight);   // 构造右子树
    root.left = builder(inLeft, index - 1);    // 构造左子树

    return root;
}

public TreeNode buildTree(int[] inorder, int[] postorder) {
     this.postorder = postorder;
     this.inorder = inorder;
     // 从后续遍历数组最后一个元素开始构造
    this.postIndex = postorder.length - 1;
    
    // 构建以（元素，下标）为键值对的哈希表
    int index = 0;
    for (Integer val : inorder) {
        indexMap.put(val, index++);
    }

    return builder(0, inorder.length - 1);
}
```

