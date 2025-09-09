# Find First and Last Position of Element in Sorted Array — Kotlin 学习样本

本页并列展示 Java 与 Kotlin 写法，强调二分上下界（lower/upper bound）模板的可复用性。

## 原始 Java 代码

```java
class Solution {
    public int[] searchRange(int[] nums, int target) {
        if (nums == null || nums.length == 0) return new int[]{-1, -1};

        int[] ret = new int[]{-1, -1};

        // 下界：第一个 >= target 的位置
        int left = 0, right = nums.length - 1;
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] < target) left = mid + 1;
            else right = mid;
        }
        if (left == right && nums[left] == target) ret[0] = left;

        // 上界：最后一个 <= target 的位置
        left = 0; right = nums.length - 1;
        while (left < right) {
            int mid = left + (right - left + 1) / 2;
            if (nums[mid] <= target) left = mid;
            else right = mid - 1;
        }
        if (left == right && nums[left] == target) ret[1] = left;

        return ret;
    }
}
```

## 等价 Kotlin 代码（逻辑不变）

```kotlin
class Solution {
    fun searchRange(nums: IntArray, target: Int): IntArray {
        val first = lowerBound(nums, target) // 第一个 >= target 的位置（半开区间写法）
        if (first == nums.size || nums[first] != target) return intArrayOf(-1, -1)
        val last = upperBound(nums, target) - 1 // 最后一个 <= target 的位置
        return intArrayOf(first, last)
    }

    private fun lowerBound(a: IntArray, x: Int): Int { // 标准下界：第一个 >= x 的下标
        var l = 0
        var r = a.size // 半开区间：[l, r)
        while (l < r) {
            val m = l + (r - l) / 2
            if (a[m] >= x) r = m else l = m + 1
        }
        return l
    }

    private fun upperBound(a: IntArray, x: Int): Int { // 标准上界：第一个 > x 的下标
        var l = 0
        var r = a.size // 半开区间：[l, r)
        while (l < r) {
            val m = l + (r - l) / 2
            if (a[m] > x) r = m else l = m + 1
        }
        return l
    }
}
```

## Kotlin 要点对照

- 模板抽象：`lowerBound`/`upperBound` 提升可读性与复用性。
- 区间选择：半开区间 `[l, r)` + 循环条件 `l < r`，边界更清晰。
- 数组类型：`IntArray` 与 `intArrayOf`，避免装箱。

## 算法说明

- `lowerBound(nums, target)` 给出第一个 `>= target` 的位置。
- `upperBound(nums, target) - 1` 给出最后一个 `<= target` 的位置。
- 时间 `O(log n)`，空间 `O(1)`。

## 思路总结

- 关键抽象：将“两次边界查找”提炼为 `lowerBound/upperBound` 模板，逻辑更稳定。
- 单调性：谓词 `a[m] >= x` 与 `a[m] > x` 在下标上单调，适合半开区间二分。
- 区间设计：使用 `[l, r)` 避免越界判断（如 `m+1`），`l==r` 时即为答案位置。
- 命中判断：若 `first` 越界或 `nums[first] != target`，说明不存在直接返回 `[-1,-1]`。
- 正确性：由上下界的定义（最小满足/第一个超过）确保定位到最左/最右目标下标。

## 简单测试（Kotlin）

```kotlin
fun main() {
    val nums = intArrayOf(5,7,7,8,8,10)
    println(Solution().searchRange(nums, 8).joinToString())  // 2, 4
    println(Solution().searchRange(nums, 6).joinToString())  // -1, -1
}
```
