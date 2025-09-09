# Find Minimum in Rotated Sorted Array（旋转数组的最小值）— Kotlin 学习样本

利用与右端元素比较的二分策略，定位最小值所在半区。

## 原始 Java 代码

```java
class Solution {
    public int findMin(int[] nums) {
        int n = nums.length;
        if (n == 1) return nums[0];
        int left = 1, right = n - 1, key = nums[0];
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] > key) left = mid + 1;
            else if (nums[mid] < key) right = mid;
        }
        return nums[left] > key ? key : nums[left];
    }
}
```

## 等价 Kotlin 代码（逻辑不变）

```kotlin
class Solution {
    fun findMin(nums: IntArray): Int {
        var l = 0
        var r = nums.lastIndex
        // 与最右元素比较，确定最小值所在半边
        while (l < r) {
            val m = l + (r - l) / 2
            if (nums[m] <= nums[r]) {
                // 右半有序，最小值在 [l, m]
                r = m
            } else {
                // 断点在右半，最小值在 [m+1, r]
                l = m + 1
            }
        }
        return nums[l]
    }
}
```

## Kotlin 要点对照

- `lastIndex` 与 `val/var` 的基础用法。
- 通过与端点比较，避免与左端比较时的细碎分支。

## 算法说明

- 若 `nums[m] <= nums[r]`，右半区有序，最小值在 `[l, m]`；否则在 `[m+1, r]`。
- 时间 `O(log n)`，空间 `O(1)`。

## 思路总结

- 旋转结构：原数组有序，旋转后仅在断点处“不连续”，最小值位于断点位置。
- 判定依据：与 `nums[r]` 比较更直观；若 `nums[m] <= nums[r]`，右半有序，最小值在左侧含 `m`。
- 收敛策略：`r = m` 或 `l = m + 1`，保持最小值始终在 `[l, r]`。
- 边界：长度为 1 的数组直接返回；算法天然处理未旋转的情形（最小值在 0）。
- 正确性：区间单调收缩 + 不变式（最小值 ∈ [l, r]）确保落点即答案。

## 简单测试（Kotlin）

```kotlin
fun main() {
    val nums = intArrayOf(4,5,6,7,0,1,2)
    println(Solution().findMin(nums)) // 0
}
```
