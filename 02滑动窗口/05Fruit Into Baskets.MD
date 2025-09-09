```kotlin
class Solution {
    fun totalFruit(fruits: IntArray): Int {
        var ans = 0
        val count = mutableMapOf<Int, Int>() // 可变 Map：果树类型 -> 计数
        var left = 0

        for (right in fruits.indices) {
            val t = fruits[right]
            count[t] = (count[t] ?: 0) + 1 // Kotlin Map 计数累加

            while (count.size > 2) { // 保持窗口中至多两种类型
                val lt = fruits[left]
                val newCnt = count[lt]!! - 1
                if (newCnt == 0) count.remove(lt) else count[lt] = newCnt
                left++
            }
            ans = maxOf(ans, right - left + 1)
        }

        return ans
    }
}
```

说明
- 简化与风格化：
  - 使用 `mutableMapOf` + 空合并 `?:` 实现计数累加；`count.size > 2` 时收缩窗口，等价但更直观。
- Kotlin 特性：
  - `mutableMapOf` 创建可变 Map；`count[lt]!!` 使用非空断言（已确保存在）；
  - `maxOf` 更新答案；`fruits.indices` 遍历数组下标。
- 集合操作速览（Map）
  - 初始化：`val m = mutableMapOf<Int, Int>()`
  - 计数累加：`m[x] = (m[x] ?: 0) + 1`
  - 计数减少与清理：`m[x] = m[x]!! - 1; if (m[x] == 0) m.remove(x)`
  - 遍历：`for ((k, v) in m) { ... }`

## Kotlin 函数式风格（等价逻辑）

```kotlin
// 递归推进右指针，并在种类超过 2 时收缩左指针；Map 仍作计数表
class SolutionFp {
    fun totalFruit(fruits: IntArray): Int {
        tailrec fun go(l: Int, r: Int, cnt: MutableMap<Int, Int>, best: Int): Int {
            if (r == fruits.size) return best
            val t = fruits[r]
            cnt[t] = (cnt[t] ?: 0) + 1
            tailrec fun shrink(ll: Int): Int {
                if (cnt.size <= 2) return ll
                val v = fruits[ll]
                val c = cnt.getValue(v) - 1
                if (c == 0) cnt.remove(v) else cnt[v] = c
                return shrink(ll + 1)
            }
            val nl = shrink(l)
            val nb = maxOf(best, r - nl + 1)
            return go(nl, r + 1, cnt, nb)
        }
        return go(0, 0, mutableMapOf(), 0)
    }
}
```

说明（FP 版）
- 思路不变：窗口内至多两种类型，超限即收缩。
- 函数式要点：递归承载窗口与计数表；无显式 while。
