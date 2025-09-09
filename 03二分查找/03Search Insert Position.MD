# Search Insert Position（搜索插入位置）— Kotlin 学习样本

本页展示“插入位置”问题的 Java 与 Kotlin 等价实现。核心是下界（lowerBound）模板：第一个 >= target 的位置。

## 原始 Java 代码

```java
class Solution {
    public int searchInsert(int[] nums, int target) {
        int left = 0, right = nums.length - 1;
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] < target) left = mid + 1;
            else right = mid;
        }
        return nums[left] < target ? left + 1 : left;
    }
}
```

## 等价 Kotlin 代码（逻辑不变）

```kotlin
class Solution {
    fun searchInsert(nums: IntArray, target: Int): Int {
        // 下界（lowerBound）：第一个 >= target 的位置，恰为插入点
        var l = 0
        var r = nums.size // 半开区间 [l, r)
        while (l < r) {
            val m = l + (r - l) / 2
            if (nums[m] >= target) r = m else l = m + 1
        }
        return l
    }
}
```

## Kotlin 要点对照

- 半开区间模板：`[l, r)` + `while (l < r)`，退出时 `l` 即最小可行下标。
- 类型与语法：`IntArray`、`val/var`、表达式式 `if`。

## 算法说明

- 目标是第一个 `>= target` 的位置；若全都小于，则落在末尾 `n`。
- 时间 `O(log n)`，空间 `O(1)`。

## 思路总结

- 目标即下界：第一个 `>= target` 的位置自然是插入点。
- 单调谓词：`nums[m] >= target` 对 m 单调，从而二分可行。
- 半开区间：`[l, r)` 令退出时 `l==r`，直接返回 `l` 无需额外分类。
- 边界覆盖：若全小于 `target`，返回 `n`；若首元素即满足，返回 0。
- 正确性：保持“答案在 `[l, r)`”的不变式，每轮都排除掉不可能部分。

## 简单测试（Kotlin）

```kotlin
fun main() {
    val nums = intArrayOf(1,3,5,6)
    println(Solution().searchInsert(nums, 5)) // 2
    println(Solution().searchInsert(nums, 2)) // 1
    println(Solution().searchInsert(nums, 7)) // 4
}
```

## Kotlin 函数式风格（等价逻辑）

```kotlin
// 尾递归实现标准 lowerBound：第一个 >= target 的位置
class SolutionFp {
    fun searchInsert(nums: IntArray, target: Int): Int = lowerBound(nums, target)

    private tailrec fun lowerBound(a: IntArray, x: Int, l: Int = 0, r: Int = a.size): Int =
        if (l >= r) l else {
            val m = l + (r - l) / 2
            if (a[m] >= x) lowerBound(a, x, l, m) else lowerBound(a, x, m + 1, r)
        }
}
```

说明（FP 版）
- 思路不变：插入点即下界位置。
- 函数式要点：半开区间 + 尾递归，无显式 while。
