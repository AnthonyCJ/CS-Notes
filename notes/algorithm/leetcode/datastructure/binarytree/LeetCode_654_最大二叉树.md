# LeetCode_654_最大二叉树

难度（medium）

---

## 方法：递归

### 分析

对于构造二叉树的问题，根节点要做的是设法将自己构造出来。本题中

1. 需要遍历数组找到最大值 maxVal
2. 根据值 maxVal 新建根节点 root
3. 然后对 maxVal左侧的子数组和右侧的子数组递归调用，构造 root 的左右子树

所以构造框架如下：

~~~java
	TreeNode constructMaximumBinaryTree(int[] nums) 
	{
		// 找到数组中的最大值
		...
		// 构造根节点
		TreeNode root = new TreeNode(maxVal);
		// 递归地构造左右子树
		root.left = constructMaximumBinaryTree([] leftpart);
		root.right = constructMaximumBinaryTree([] rightpart);
		return root;
	}
~~~

填充细节

~~~java
	TreeNode constructMaximumBinaryTree(int[] nums) 
	{
		// 找到数组中的最大值
		int maxVal = Integer.MIN_VALUE;
		int index = 0;
		for (int i = 0; i < nums.length; i++)
		{
			if (nums[i] > maxVal)
			{
				maxVal = nums[i];
				index = i;
			}
		}
		// 构造根节点
		TreeNode root = new TreeNode(maxVal);
		// 递归地构造左右子树
		root.left = constructMaximumBinaryTree(nums[0 ... index - 1]);
		root.right = constructMaximumBinaryTree(nums[index + 1 ... nums.length - 1]);
		return root;
	}
~~~

最后将函数参数修改为 数组、左临界下标、右临界下标，实现递归算法。

### 复杂度分析

* 时间复杂度：O(n)，遍历所有节点。
* 空间复杂度：O(n)，由系统维护函数栈。最坏情况为O(n)，树呈链状；最好为O(logn)，完全二叉树。

### 代码实现

```java
public TreeNode constructMaximumBinaryTree(int[] nums) {
    // 以数组完整区间作为起始状态调用递归构造函数
    return constructBinaryTree(nums, 0, nums.length - 1);
}

/**
 * 递归_构造最大二叉树
 * Version 1.0 2021-07-20 by XCJ
 * @param nums int[] 存储节点值
 * @param low 数组查询区间下界
 * @param high 数组查询区间上界
 * @return TreeNode root 最大二叉树根节点
 */
public TreeNode constructBinaryTree(int[] nums, int low, int high) {
    // 递归出口
    if (low > high) {
        return null;
    }
    // 找到数组中的最大值
    int maxValIndex = -1, maxVal = Integer.MIN_VALUE;
    for (int i = low; i <= high; i++) {
        if (maxVal < nums[i]) {
            maxValIndex = i;
            maxVal = nums[i];
        }
    }
    /* 前序遍历位置 */
    // 构造根节点
    TreeNode root = new TreeNode(maxVal);
    // 递归地构造左右子树
    root.left = constructBinaryTree(nums, low, maxValIndex - 1);
    root.right = constructBinaryTree(nums, maxValIndex + 1, high);

    return root;
}
```