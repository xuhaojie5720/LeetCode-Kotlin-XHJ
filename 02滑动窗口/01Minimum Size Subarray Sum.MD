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

## Kotlin 函数式风格（等价逻辑）

```kotlin
// 尾递归推进右指针，内嵌递归收缩左指针；保持 O(n)/O(1)
class SolutionFp {
    fun minSubArrayLen(target: Int, nums: IntArray): Int {
        val n = nums.size
        if (n == 0) return 0
        tailrec fun go(l: Int, r: Int, sum: Int, best: Int): Int {
            if (r == n) return if (best == Int.MAX_VALUE) 0 else best
            val s = sum + nums[r]
            tailrec fun shrink(ll: Int, ss: Int, bb: Int): Triple<Int, Int, Int> =
                if (ll > r || ss < target) Triple(ll, ss, bb)
                else shrink(ll + 1, ss - nums[ll], minOf(bb, r - ll + 1))
            val (nl, ns, nb) = shrink(l, s, best)
            return go(nl, r + 1, ns, nb)
        }
        return go(0, 0, 0, Int.MAX_VALUE)
    }
}
```

说明（FP 版）
- 思路不变：右扩张、左收缩；以递归参数承载窗口与最优值。
- 函数式要点：消除显式 while/for，仍为线性时间、常数空间。
