# Find Pivot Index（寻找中心索引）— Kotlin 学习样本

本页将同一算法的 Java 与 Kotlin 写法并列展示，帮助理解从 Java 迁移到 Kotlin 时的等价表达；算法与复杂度保持完全一致，不做任何逻辑改动。

## 原始 Java 代码

```java
class Solution {
    public int pivotIndex(int[] nums) {
        int n = nums.length;
        int totalSum = 0;
        for (int num : nums) totalSum += num;

        int subSum = 0;
        for (int i = 0; i < n; i++) {
            if (i > 0) subSum += nums[i - 1];
            if (subSum == (totalSum - nums[i] - subSum)) {
                return i;
            }
        }
        return -1;
    }
}
```

## 等价 Kotlin 代码（逻辑不变）

```kotlin
class Solution {
    fun pivotIndex(nums: IntArray): Int {
        val n = nums.size
        val totalSum = nums.sum() // Kotlin：集合扩展 sum()
        var leftSum = 0
        for (i in 0 until n) { // 半开区间 [0, n)
            val rightSum = totalSum - nums[i] - leftSum
            if (leftSum == rightSum) return i
            leftSum += nums[i]
        }
        return -1
    }
}
```

## Kotlin 要点对照

- 数组类型：`IntArray` 对应 Java 的 `int[]`。
- 聚合函数：`nums.sum()` 简化总和计算；其复杂度与显式循环相同。
- 区间遍历：`for (i in 0 until n)` 更直观，`until` 为半开区间。

## 算法说明

- 思想：中心索引左侧和右侧元素之和相等；维护左和 `leftSum`，右和可由 `total - nums[i] - leftSum` 得出。
- 复杂度：时间 `O(n)`，空间 `O(1)`。

## 思路总结

- 模板：一次扫描维护前缀和；避免重复求和带来的 `O(n^2)` 退化。
- 不变式：`leftSum` 始终等于 `sum(nums[0..i-1])`。
- 边界：索引 0 和 n-1 自然覆盖；若无解返回 -1。

## 简单测试（Kotlin）

```kotlin
fun main() {
    println(Solution().pivotIndex(intArrayOf(1,7,3,6,5,6))) // 3
    println(Solution().pivotIndex(intArrayOf(1,2,3)))       // -1
}
```

## Kotlin 函数式风格（等价逻辑）

```kotlin
// 用 foldIndexed 聚合左和并在首次命中时记录答案
class SolutionFp {
    fun pivotIndex(nums: IntArray): Int {
        val total = nums.sum()
        return nums.indices.fold(-1 to 0) { (ans, left), i ->
            if (ans != -1) ans to left // 已找到，保持不变
            else {
                val right = total - nums[i] - left
                if (left == right) i to (left + nums[i]) else -1 to (left + nums[i])
            }
        }.first
    }
}
```

说明（FP 版）
- 思路不变：`left` 与 `total-left-nums[i]` 比较。
- 函数式要点：累积器携带“答案/左和”，无外部可变变量。
