```kotlin
// 位运算：用 26 位 bitmask 记录是否出现过（假设均为小写字母 a..z）
class Solution {
    fun isUnique(astr: String): Boolean {
        if (astr.length > 26) return false // 鸽巢原理：字符数超 26 必有重复

        var mask = 0
        for (ch in astr) {
            val bit = 1 shl (ch - 'a')
            if ((mask and bit) != 0) return false // 已出现
            mask = mask or bit
        }
        return true
    }
}
```

说明
- 思路：位掩码的第 k 位表示字母 `('a' + k)` 是否出现，重复则该位已为 1。
- 复杂度：时间 O(n)，空间 O(1)。
- Kotlin 特性：`shl/and/or` 位运算；`for (ch in astr)` 直接遍历字符；`if` 作为表达式早返回。
- 备注：若字符集为扩展 ASCII/Unicode，应改为布尔数组或 `BitSet`，此处按题意放在位运算类别，用 26 位掩码实现。
```java
class Solution {
    public boolean isUnique(String astr) {
        if(astr.length()>26) return false;

        boolean[] chars=new boolean[26];
        for(char ch:astr.toCharArray()) {
            if(chars[ch-'a']) {
                return false;
            }
            chars[ch-'a']=true;
        }

        return true;
    }
}
```

```kotlin
// Kotlin 函数式（FP）风格：使用 fold 累积 bitmask，保持表达式风格
class SolutionFp {
    fun isUnique(astr: String): Boolean {
        if (astr.length > 26) return false
        // fold 返回最终掩码；在发现重复时直接提前返回 false
        astr.fold(0) { acc, ch ->
            val bit = 1 shl (ch - 'a')
            if ((acc and bit) != 0) return false
            acc or bit
        }
        return true
    }
}
```

补充说明
- 思路同位掩码：第 k 位标记是否见过 `('a'+k)`。
- FP 特点：用 `fold` 聚合状态，函数体以表达式为主、无外部可变状态；利用“提前返回”避免嵌套。
