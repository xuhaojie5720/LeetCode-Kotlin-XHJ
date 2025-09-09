# Matrix Block Sum（矩阵区域和）— Kotlin 学习样本

本页并列展示 Java 与 Kotlin 写法，保持原有“直接求和”的逻辑与复杂度不变（可选优化为二维前缀和，此处不改动）。

## 原始 Java 代码

```java
class Solution {
    public int[][] matrixBlockSum(int[][] mat, int k) {
        int m = mat.length;
        int n = mat[0].length;
        int[][] answer = new int[m][n];
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                int row1 = (i - k) > 0 ? i - k : 0;
                int col1 = (j - k) > 0 ? j - k : 0;
                int row2 = (i + k) >= m ? m - 1 : i + k;
                int col2 = (j + k) >= n ? n - 1 : j + k;
                for (int ii = row1; ii <= row2; ii++) {
                    for (int jj = col1; jj <= col2; jj++) {
                        answer[i][j] += mat[ii][jj];
                    }
                }
            }
        }
        return answer;
    }
}
```

## 等价 Kotlin 代码（逻辑不变）

```kotlin
class Solution {
    fun matrixBlockSum(mat: Array<IntArray>, k: Int): Array<IntArray> {
        val m = mat.size
        val n = mat[0].size
        val ans = Array(m) { IntArray(n) }
        for (i in 0 until m) {
            for (j in 0 until n) {
                val r1 = maxOf(0, i - k) // Kotlin：maxOf/minOf 简化边界
                val c1 = maxOf(0, j - k)
                val r2 = minOf(m - 1, i + k)
                val c2 = minOf(n - 1, j + k)
                var sum = 0
                for (ii in r1..r2) {
                    for (jj in c1..c2) {
                        sum += mat[ii][jj]
                    }
                }
                ans[i][j] = sum
            }
        }
        return ans
    }
}
```

## Kotlin 要点对照

- 二维数组：`Array<IntArray>` 对应 Java 的 `int[][]`；构造 `Array(m) { IntArray(n) }`。
- 区间与边界：`..` 闭区间、`maxOf/minOf` 处理边界更直观。

## 算法说明

- 对每个格子 `(i,j)`，枚举其 `k` 距离曼哈顿方块内的所有格子求和（原逻辑）。
- 复杂度：时间 `O(m*n*k*k)`，空间 `O(1)` 额外（不含输出）。
- 可选优化：二维前缀和将复杂度降为 `O(m*n)`。

## 思路总结

- 直接枚举在范围内的行列并累加，边界通过 `maxOf/minOf` 收紧到合法范围。
- 若需加速，二维前缀和 `pre[i+1][j+1]` 支持常数时间查询任意矩形和。

## 简单测试（Kotlin）

```kotlin
fun main() {
    val mat = arrayOf(
        intArrayOf(1,2,3),
        intArrayOf(4,5,6),
        intArrayOf(7,8,9)
    )
    val res = Solution().matrixBlockSum(mat, 1)
    println(res.joinToString(" | ") { it.joinToString() })
}
```

## Kotlin 函数式风格（等价逻辑）

```kotlin
// 直接求和版的函数式改写：使用区间 + sumOf 表达矩形内求和
class SolutionFp {
    fun matrixBlockSum(mat: Array<IntArray>, k: Int): Array<IntArray> {
        val m = mat.size
        val n = mat[0].size
        return Array(m) { i ->
            IntArray(n) { j ->
                val r1 = maxOf(0, i - k)
                val r2 = minOf(m - 1, i + k)
                val c1 = maxOf(0, j - k)
                val c2 = minOf(n - 1, j + k)
                (r1..r2).sumOf { ii -> (c1..c2).sumOf { jj -> mat[ii][jj] } }
            }
        }
    }
}
```

说明（FP 版）
- 思路不变：仍为直接枚举区域求和；仅以表达式化方式呈现。
- 函数式要点：区间与 `sumOf` 组合，去除显式嵌套循环与临时变量。
