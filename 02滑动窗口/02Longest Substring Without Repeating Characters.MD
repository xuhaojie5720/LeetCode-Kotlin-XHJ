```kotlin
class Solution {
    fun lengthOfLongestSubstring(s: String): Int {
        val seen = mutableSetOf<Char>() // 可变 Set：维护当前窗口内的字符
        var left = 0
        var maxLen = 0

        for (right in s.indices) { // indices：0..s.lastIndex 下标范围
            val c = s[right]
            while (c in seen) { // in：集合包含判断
                seen.remove(s[left]) // 收缩左边界直到不包含重复
                left++
            }
            seen.add(c)
            maxLen = maxOf(maxLen, right - left + 1)
        }

        return maxLen
    }
}
```

说明
- 简化与风格化：使用 Set 作为滑窗的成员检查结构，遇到重复时通过 while 收缩左边界直至无重复；实现直观，时间复杂度仍为 O(n)。
- 与 Map 对比：Map 可一次将 `left` 跳到上次位置 + 1，常数因子略优；Set 可读性更好，便于记忆与书写。
- Kotlin 特性：
  - `mutableSetOf<Char>()` 创建可变集合；`c in seen` 语法糖；
  - `s.indices` 遍历字符串下标；`maxOf` 更新窗口最大长度。
- 集合操作速览（Set）
  - 初始化：`val seen = mutableSetOf<Char>()`
  - 操作：`seen.add(x)`, `seen.remove(x)`, `x in seen`
  - 遍历：`for (ch in seen) { ... }`
  - 其他：`seen.clear()`, `seen.size`

## Kotlin 函数式风格（等价逻辑）

```kotlin
// 用尾递归推进窗口；Set 仍作为成员检查结构，保持 O(n)/O(1) 额外空间
class SolutionFp {
    fun lengthOfLongestSubstring(s: String): Int {
        tailrec fun go(l: Int, r: Int, seen: MutableSet<Char>, best: Int): Int {
            if (r == s.length) return best
            val c = s[r]
            return if (c !in seen) {
                seen.add(c)
                go(l, r + 1, seen, maxOf(best, r - l + 1))
            } else {
                seen.remove(s[l])
                go(l + 1, r, seen, best)
            }
        }
        return go(0, 0, mutableSetOf(), 0)
    }
}
```

说明（FP 版）
- 思路不变：遇重复收缩左侧，否则扩张右侧并刷新答案。
- 函数式要点：递归承载窗口状态；无外层可变变量。
