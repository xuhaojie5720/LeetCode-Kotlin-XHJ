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

## Kotlin 函数式风格（等价逻辑）

```kotlin
// 递归推进右指针，并在 zeros>k 时递归收缩左指针
class SolutionFp {
    fun longestOnes(nums: IntArray, k: Int): Int {
        val n = nums.size
        tailrec fun go(l: Int, r: Int, zeros: Int, best: Int): Int {
            if (r == n) return best
            val z = zeros + if (nums[r] == 0) 1 else 0
            tailrec fun shrink(ll: Int, zz: Int): Pair<Int, Int> =
                if (zz <= k) ll to zz else shrink(ll + 1, zz - if (nums[ll] == 0) 1 else 0)
            val (nl, nz) = shrink(l, z)
            return go(nl, r + 1, nz, maxOf(best, r - nl + 1))
        }
        return go(0, 0, 0, 0)
    }
}
```

说明（FP 版）
- 思路不变：窗口内 0 的个数不超过 k；保持 O(n)/O(1)。
- 函数式要点：用递归替代 while，状态通过参数累积。
