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
