```java
class Solution {
    public int missingNumber(int[] nums) {
        int totalNumsSum = java.util.Arrays.stream(nums).sum();
        int n = nums.length;
        int totalSum = (n + 0) * (n + 1) / 2;
        return totalSum - totalNumsSum;
    }
}
```

```kotlin
// 位运算：异或消元（0..n 与 nums 全部异或，成对消掉，剩下缺失值）
class Solution {
    fun missingNumber(nums: IntArray): Int {
        var res = nums.size // 先带上 n（因为 i 只到 n-1）
        for (i in nums.indices) {
            res = res xor i xor nums[i]
        }
        return res
    }
}
```

说明
- 思路：利用异或性质 `x xor x = 0`、`x xor 0 = x`，将 `0..n` 与 `nums` 中出现的数两两抵消，剩下的即缺失数。
- 复杂度：时间 O(n)，空间 O(1)。
- Kotlin 特性：`xor` 位运算；`nums.indices` 下标遍历；`nums.size` 获取 n。

```kotlin
// Kotlin 函数式（FP）风格：用 fold 聚合异或值，保持不可变表达式
class SolutionFp {
    fun missingNumber(nums: IntArray): Int =
        nums.indices.fold(nums.size) { acc, i -> acc xor i xor nums[i] }
}
```

补充说明
- 同逻辑：仍是“从 0..n 与数组一一异或，成对抵消”。
- FP 特点：`fold` 聚合、无显式可变变量；函数以单表达式返回。
