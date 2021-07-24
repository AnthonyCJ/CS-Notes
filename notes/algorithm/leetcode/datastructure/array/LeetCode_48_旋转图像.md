# LeetCode_48_旋转图像

难度（medium）

标签：数组、数学

---

## 方法：用翻转代替旋转

### 思路

题目要求原地将二维数组顺时针旋转90°。旋转公式如下

~~~java
	int n = matrix.length;
	matrix[rol][col] <==> matrix[col][n - 1 - rol];
~~~

通过观察规律可发现，将矩阵水平翻转一次，再主对角线翻转一次，联立可得出旋转公式。

~~~java
	matrix[rol][col] <==> matrix[n - 1 - rol][col];
	matrix[n - 1 - rol][col] <==> matrix[col][n - 1 - rol];	// 联立，得
	matrix[rol][col] <==> matrix[col][n - 1 - rol];
~~~

### 复杂度分析

* 时间复杂度：O(n2)，其中 n 是矩阵的长。
* 空间复杂度：O(1)。

### 代码实现

```java
/**
 * 顺时针旋转矩阵90°
 * Version 1.0 2021-07-24 by XCJ
 * @param matrix 待旋转矩阵
 */
public void rotate(int[][] matrix) {
    int n = matrix.length;
    // 水平翻转
    for (int i = 0; i < n / 2; i++)
        for (int j = 0; j < n; j++) {
            int temp = matrix[i][j];
            matrix[i][j] = matrix[n - 1 - i][j];
            matrix[n - 1 - i][j] = temp;
        }
    // 对角线翻转
    for (int i = 0; i < n; i++)
        for (int j = i + 1; j < n; j++) {
            int temp = matrix[i][j];
            matrix[i][j] = matrix[j][i];
            matrix[j][i] = temp;
        }
}
```