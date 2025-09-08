```kotlin
class Solution {
    fun longestOnes(nums: IntArray, k: Int): Int { // IntArray：原生整型数组
        var left = 0
        var zeros = 0 // 当前窗口中 0 的个数
        var result = 0

        for (right in nums.indices) { // indices: 0..nums.lastIndex
            if (nums[right] == 0) zeros++
            while (zeros > k) { // 超过可翻转数量，收缩左边界
                if (nums[left] == 0) zeros--
                left++
            }
            result = maxOf(result, right - left + 1)
        }

        return result
    }
}
```

说明
- 简化与风格化：
  - 采用更直观的滑窗写法：以 `zeros` 统计窗口中 0 的数量并在 `zeros > k` 时收缩窗口，逻辑等价但更简洁。
- Kotlin 特性：
  - `nums.indices` 遍历数组下标；`maxOf` 计算窗口长度最大值。
- 复杂度保持：时间 O(n)，空间 O(1)。
