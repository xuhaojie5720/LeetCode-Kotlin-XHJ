# Find Peak Element（寻找峰值）— Kotlin 学习样本

与“山脉数组找峰”相同的爬坡二分思想，任意数组也必存在峰值。

## 原始 Java 代码

```java
class Solution {
    public int findPeakElement(int[] nums) {
        int n = nums.length;
        if (n == 1) return 0;
        if (n == 2) return nums[0] > nums[1] ? 0 : 1;

        int left = 0, right = n - 1;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            boolean isPeak =
                (mid == 0 && nums[0] > nums[1]) ||
                (mid == n - 1 && nums[n - 1] > nums[n - 2]) ||
                (mid > 0 && mid < n - 1 && nums[mid] > nums[mid - 1] && nums[mid] > nums[mid + 1]);
            if (isPeak) return mid;
            else if (nums[mid] < nums[mid + 1]) left = mid + 1;
            else right = mid - 1;
        }
        return -1;
    }
}
```

## 等价 Kotlin 代码（逻辑不变）

```kotlin
class Solution {
    fun findPeakElement(nums: IntArray): Int {
        var l = 0
        var r = nums.lastIndex
        // 任意数组中必有峰值；若 nums[m] < nums[m+1]，峰在右侧，否则在左侧（含 m）
        while (l < r) {
            val m = l + (r - l) / 2
            if (nums[m] < nums[m + 1]) l = m + 1 else r = m
        }
        return l
    }
}
```

## Kotlin 要点对照

- `lastIndex` 获取末尾下标；`val` 临时变量；紧凑条件写法。
- 用趋势判断方向，避免复杂边界判定。

## 算法说明

- 若 `nums[m] < nums[m+1]`，峰在右；否则在左（含 m）。
- 时间 `O(log n)`，空间 `O(1)`。

## 思路总结

- 关键观察：若 `nums[m] < nums[m+1]`，右侧存在上升，必能到达某个峰；否则左侧存在峰。
- 收敛方式：始终向更高的一侧推进，保证有“上坡”的方向上一定有峰。
- 边界处理：`l<r` 确保 `m+1` 有效；最终 `l==r` 时即某个峰的位置。
- 允许多峰：算法返回任一峰即可，符合题意；不依赖全局唯一峰的性质。
- 正确性：数组两端视作负无穷的常见假设下，总存在峰，爬坡二分必达。

## 简单测试（Kotlin）

```kotlin
fun main() {
    val nums = intArrayOf(1,2,1,3,5,6,4)
    println(Solution().findPeakElement(nums)) // 5 或 1（返回任意一个峰）
}
```
