```java
class Solution {
    public int getSum(int a, int b) {
        if (b == 0) return a;
        return getSum(a ^ b, (a & b) << 1);
    }
}
```

```kotlin
// 位运算加法：无进位和 + 进位，迭代直至进位为 0
class Solution {
    fun getSum(a: Int, b: Int): Int {
        var x = a
        var y = b
        while (y != 0) {
            val carry = (x and y) shl 1 // 进位
            x = x xor y                 // 无进位和
            y = carry                   // 新一轮进位
        }
        return x
    }
}
```

说明
- 思路：`a xor b` 等于逐位相加不进位；`(a and b) << 1` 是进位，重复累计直至进位为 0。
- 复杂度：固定 32 次内收敛，时间 O(1)，空间 O(1)。
- Kotlin 特性：`and/xor/shl` 位运算；`while` 循环；局部可变变量 `var`。

```kotlin
// Kotlin 函数式（FP）风格：尾递归实现，避免显式可变变量
class SolutionFp {
    tailrec fun getSum(a: Int, b: Int): Int =
        if (b == 0) a else getSum(a xor b, (a and b) shl 1)
}
```

补充说明
- 同逻辑：仍是“无进位和 + 进位”的位运算加法。
- FP 特点：使用 `tailrec` 尾递归，表达式风格、无显式循环与可变状态。
