```kotlin
class Solution {
    private fun isAnagram(s1: String, s2: String): Boolean { // 计数法判断是否为异位词
        if (s1.length != s2.length) return false
        val cnt = IntArray(26) // 原生整型数组
        for (ch in s1) cnt[ch - 'a']++ // 字符运算：Char 可参与减法
        for (ch in s2) cnt[ch - 'a']--
        for (v in cnt) if (v != 0) return false
        return true
    }

    fun findAnagrams(s: String, p: String): List<Int> {
        val chars = s.toCharArray()
        val ans = mutableListOf<Int>() // 可变 List：存放所有起始下标
        var left = 0
        var right = 0

        while (right < s.length) { // 保持窗口长度与 p 相同
            while (right < s.length && right - left + 1 < p.length) {
                right++
            }
            if (right < s.length) {
                val sub = String(chars, left, right - left + 1) // 从 char[] 构造子串
                if (isAnagram(sub, p)) ans.add(left)
                left++
                right++
            }
        }

        return ans
    }
}
```

说明
- 与原逻辑一致：仍按固定窗口长度逐个窗口调用计数校验函数 `isAnagram`，时间复杂度 O(26·n) ≈ O(n)。
- Kotlin 特性：
  - `IntArray` 作计数桶；`for (ch in s)` 直接遍历字符；
  - `mutableListOf<Int>()` 创建可变列表；`String(chars, offset, length)` 从 `CharArray` 构造子串。
- 集合操作速览（List）
  - 初始化：`val L = mutableListOf<Int>()`
  - 添加：`L.add(x)`；访问：`L[i]`
  - 遍历：`for (x in L) { ... }` 或 `L.forEach { ... }`
