# LeetCode_01.08._零矩阵

难度（medium）

## 方法1：使用标记数组

### 思路

申请两个 boolean 类型标识数组 rol[] 和 col[]，rol存放行标识，col[]存放列标识。第一次遍历 matrix 将含0的坐标记录至标识数组，第二次遍历 matrix 根据标识数组清零元素。

* 如果矩阵元素为0，标记数组对应元素赋值为 true。

### 复杂度分析

* 时间复杂度：O(mn)，两次遍历矩阵的开销。m 为矩阵的行数，n 为矩阵的列数。
* 空间复杂度：O(m + n)，两个标识数组。

### 代码实现

~~~java
public void setZeroes(int[][] matrix) {
    int m = matrix.length;		// 矩阵行数
    int n = matrix[0].length;	// 矩阵列数
    boolean[] row = new boolean[m];
    boolean[] col = new boolean[n];
    // 记录位置
    for (int i = 0; i < m; i++) 
        for (int j = 0; j < n; j++) {
            if (matrix[i][j] == 0) {
                row[i] = col[j] = true;
            }
        }
    // 遍历，根据标识数组置0
    for (int i = 0; i < m; i++)
        for (int j = 0; j < n; j++) {
            if (row[i] || col[j]) {
                matrix[i][j] = 0;
            }
        }
}
~~~

<br>

## 方法2：使用两个变量优化空间（方法一改进）

### 思路

方法一使用了两个数组以坐标的形式存储每个矩阵元素的**为0状态**，本方法尝试将矩阵原空间的第一行第一列作为标志数组存储。要使用第一行和第一列，就需要在使用前记录第一行第一列的状态（即第一行第一列是否含有0元素），所以先分别扫描矩阵第一行和第一列是否有0元素，并使用<font color="ff9f44">两个 boolean 变量</font>记录。记录后，即可使用第一行和第一列作为标志数组记录其它位置的信息。最后根据**两个标志数组**和**两个 boolean 变量**进行矩阵元素置0处理。

### 步骤

> 个人认为，用**进栈**、**出栈**的思想考虑检测和处理的顺序比较易于理解。

整理如下：

1. 分别扫描矩阵第0行、第0列，结果存入 boolean **flagRow0**, **flagCol0**。<font color="ff9f44">（Push）</font>
2. 使用矩阵第0行、第0列记录其它位置的含0状态。<font color="ff9f44">（Push）</font>
3. 通过第0行、第0列信息处理其它位置的置0操作。<font color="ff9f44">（Pop）</font>
4. 通过boolean flagRow, flagCol处理第0行、第0列的置0操作。<font color="ff9f44">（Pop）</font>

### 复杂度分析

* 时间复杂度：O(mn)，其中 m 是矩阵的行数，n是矩阵的列数。我们至多只需要遍历该矩阵两次。
* 空间复杂度：O(1)，需要 2 个变量空间。

### 代码实现

~~~ java
public void setZeroes(int[][] matrix) {
    int m = matrix.length;
    int n = matrix[0].length;
    boolean flagRow0 = false, flagCol0 = false;
    // 扫描记录第一列
    for (int i = 0; i < m; i++) {
        if (matrix[i][0] == 0) {
            flagCol0 = true;
            break;
        }
    }
    // 扫描记录第一行
    for (int j = 0; j < n; j++) {
        if (matrix[0][j] == 0) {
            flagRow0 = true;
            break;
        }
    }
    // 使用第一行第一列记录其它位置信息
    for (int i = 1; i < m; i++)
        for (int j = 1; j < n; j++) {
            if (matrix[i][j] == 0) {
                matrix[0][j] = 0;
                matrix[i][0] = 0;
            }
        }
    // 使用第一行第一列处理其它位置信息
    for (int i = 1; i < m; i++)
        for (int j = 1; j < n; j++) {
            if (matrix[i][0] == 0 || matrix[0][j] == 0) {
                matrix[i][j] = 0;
            }
        }
    // 处理第一行和第一列
    if(flagRow0) {
        for (int j = 0; j < n; j++) {
            matrix[0][j] = 0;
        }
    }
    if(flagCol0) {
        for (int i = 0; i < m; i++) {
            matrix[i][0] = 0;
        }
    }        
}
~~~

<br>

## 方法3：使用1个变量（对方法2继续空间优化）

### 思路

记录除第一行第一列外剩余元素的为0状态时，实际只用到了第一行、第一列从下标1开始的位置，所以 `matrix[0][0]` 的位置被空余出来，可将其作为 **flagRow0**。

### 复杂度分析

* 时间复杂度：O(mn)，其中 m 是矩阵的行数，n是矩阵的列数。我们至多只需要遍历该矩阵两次。
* 空间复杂度：O(1)，需要 1 个变量空间。

### 代码实现

~~~java
public void setZeroes(int[][] matrix) {
    int m = matrix.length;
    int n = matrix[0].length;
    boolean flagCol0 = false;   // 列
    // 扫描记录第一列
    for (int i = 0; i < m; i++) {
        if (matrix[i][0] == 0) {
            flagCol0 = true;   // flag of row[0]
            break;
        }
    }
    // 扫描记录第一行
    for (int j = 0; j < n; j++) {
        if (matrix[0][j] == 0) {
            matrix[0][0] = 0;
            break;
        }
    }
    // 使用第一行第一列记录其它位置信息
    for (int i = 1; i < m; i++)
        for (int j = 1; j < n; j++) {
            if (matrix[i][j] == 0) {
                matrix[0][j] = 0;
                matrix[i][0] = 0;
            }
        }
    // 使用第一行第一列处理其它位置信息
    for (int i = 1; i < m; i++)
        for (int j = 1; j < n; j++) {
            if (matrix[i][0] == 0 || matrix[0][j] == 0) {
                matrix[i][j] = 0;
            }
        }
    // 处理第一行和第一列
    if(matrix[0][0] == 0) {
        for (int j = 1; j < n; j++) {
            matrix[0][j] = 0;
        }
    }
    if(flagCol0) {
        for (int i = 0; i < m; i++) {
            matrix[i][0] = 0;
        }
    }        
}
~~~

> PS：如果改变一下处理顺序，能精简一下处理部分的代码。为了防止每一列的第一个元素被提前更新，我们需要从最后一行开始，倒序地处理矩阵元素。具体实现如下。

~~~java
public void setZeroes(int[][] matrix) {
    int m = matrix.length, n = matrix[0].length;
    boolean flagCol0 = false;
    // 记录位置信息
    for (int i = 0; i < m; i++) {
        if (matrix[i][0] == 0) {
            flagCol0 = true;
        }
        for (int j = 1; j < n; j++) {
            if (matrix[i][j] == 0) {
                matrix[i][0] = matrix[0][j] = 0;
            }
        }
    }
    // 根据位置信息 行倒序处理矩阵
    for (int i = m - 1; i >= 0; i--) {
        for (int j = 1; j < n; j++) {
            if (matrix[i][0] == 0 || matrix[0][j] == 0) {
                matrix[i][j] = 0;
            }
        }
        if (flagCol0) {
            matrix[i][0] = 0;
        }
    }
}
~~~
