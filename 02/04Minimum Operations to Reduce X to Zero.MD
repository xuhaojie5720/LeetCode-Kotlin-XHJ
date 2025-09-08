```kotlin
// 滑动窗口：不需要 Map（本题 nums 都是正数）
class Solution {
    fun minOperations(nums: IntArray, x: Int): Int {
        val total = nums.sum()
        if (total < x) return -1
        if (total == x) return nums.size

        val target = total - x
        var left = 0
        var sum = 0
        var maxLen = -1

        for (right in nums.indices) {
            sum += nums[right]
            while (sum > target && left <= right) {
                sum -= nums[left++]
            }
            if (sum == target) {
                maxLen = maxOf(maxLen, right - left + 1)
            }
        }

        return if (maxLen == -1) -1 else nums.size - maxLen
    }
}
```

说明
- 不需要 Map：因为数组元素为正数，寻找和为 `target = total - x` 的最长连续子数组可以用双指针滑动窗口，时间 O(n)、空间 O(1)。
- 维护窗口和：右指针扩张加入元素；若 `sum > target`，左指针收缩直到 `sum <= target`；当 `sum == target` 时更新最长长度。
- 最终答案：从两端移除元素的最小操作数 = `nums.size - maxLen`。
- 边界：`total < x` 不可能；`total == x` 说明必须移除所有元素，直接返回 `nums.size`。
