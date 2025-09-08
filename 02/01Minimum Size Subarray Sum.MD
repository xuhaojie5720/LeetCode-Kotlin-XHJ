```kotlin
class Solution {
    fun minSubArrayLen(target: Int, nums: IntArray): Int { // IntArray：原生整型数组
        val n = nums.size
        var result = n + 1
        var left = 0
        var sum = 0

        for (right in 0 until n) { // until：半开区间 [0, n)
            sum += nums[right]
            while (left <= right && sum >= target) {
                result = minOf(result, right - left + 1) // Kotlin 标准库 minOf
                sum -= nums[left]
                left++
            }
        }

        return if (result == n + 1) 0 else result // if 作为表达式
    }
}
```

说明
- 简化处理：使用 `for (right in 0 until n)` 与 `minOf` 替代 Java 的下标自增与 `Math.min`；三元运算改为 Kotlin `if` 表达式。
- Kotlin 特性：
  - `val`/`var` 明确可变性；`IntArray` 使用原生数组；
  - `until` 创建半开区间；`minOf`/`maxOf` 为标准库函数；
  - `if` 可作表达式直接返回值。
- 复杂度保持：时间 O(n)，空间 O(1)，与原实现一致。
