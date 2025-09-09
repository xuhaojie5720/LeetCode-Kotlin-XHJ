```java
class Solution {
    public int singleNumber(int[] nums) {
        int n = nums.length;
        int answer = 0;

        for (int i = 0; i < 32; i++) {
            int bitSum = 0;
            for (int j = 0; j < n; j++) {
                bitSum += (nums[j] >>> i) & 1;
            }
            answer |= (bitSum % 3) << i;
        }

        return answer;
    }
}
```

```kotlin
// 位运算 FSM：ones/twos 记录 (mod 3) 的状态转移
class Solution {
    fun singleNumber(nums: IntArray): Int {
        var ones = 0
        var twos = 0
        for (x in nums) {
            ones = (ones xor x) and twos.inv()
            twos = (twos xor x) and ones.inv()
        }
        return ones
    }
}
```

```kotlin
// Kotlin 函数式（FP）风格：fold 聚合 (ones, twos) 状态，表达式返回
class SolutionFp {
    fun singleNumber(nums: IntArray): Int =
        nums.fold(0 to 0) { (ones, twos), x ->
            val newOnes = (ones xor x) and twos.inv()
            val newTwos = (twos xor x) and newOnes.inv()
            newOnes to newTwos
        }.first
}
```

说明
- 思路：每一位上，出现 3 次的比特会被状态机抵消，最终剩下只出现 1 次的比特（答案）。
- 等价法：也可逐位统计 0..31 位上出现 1 的个数对 3 取模再重建答案。
- 复杂度：时间 O(n)，空间 O(1)。
- Kotlin 特性：`xor/and/inv` 位运算；增强 for 遍历 `for (x in nums)`。
