# LeetCode_724_寻找数组的中心下标

难度（easy）

<img src="LeetCode_724.png" style="zoom:150%;" />

---

## 思路

两次遍历实现。第一次遍历求和 total，第二次遍历寻找中点。寻找中点的过程中，用一个指针指向处理位置，此时数组分为三部分，指针左侧（sum = nums[0] + ... + sum[ptr-1]，指针右侧 = total - （nums[0] + ... + sum[ptr-1] + sum[ptr]） = total - sum - nums[ptr]。遍历过程动态更新即可。

## 细节处理

注意迭代操作的顺序安排。对左侧和 sum 的自增放在比较大小后可以精简特殊情况的判断过程。如果放在最前面需要将 nums[0] 单独处理。

## 复杂度分析

* 时间复杂度：O(n)。两趟遍历数组。
* 空间复杂度：O(1)

## 代码实现

~~~java
    /**
     * 寻找数组中心下标
     * Version 1.0 2021-07-21 by XCJ
     * Time: O(n), Space: O(1)
     * @param nums 目标数组
     * @return int 中心下标，未找到返回 -1
     */
    public int pivotIndex(int[] nums) {
        // 求和
        int total = 0;
        for (int val : nums) {
            total += val;
        }

        // 找中点
        int sum = 0;
        for (int i = 0; i < nums.length; i++) {
            if (sum == (total -= nums[i])) {
                return i;
            }
            sum += nums[i];
        }
        return -1;
    }
~~~

