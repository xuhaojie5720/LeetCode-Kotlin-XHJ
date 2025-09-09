```java
class Solution {
    public int[] missingTwo(int[] nums) {
        if(nums==null) {
            return new int[0];
        }

        int n=nums.length;
        int N=n+2;

        int xorSum=0;
        for(int i=1;i<=N;i++) {
            xorSum^=i;
        }
        for(int i=0;i<n;i++) {
            xorSum^=nums[i];
        }

        int lowBit=(xorSum & (-xorSum));

        int answer1=0;
        int answer2=0;
        for(int i=1;i<=N;i++) {
            if((i & lowBit)==0) {
                answer1^=i;
            }else {
                answer2^=i;
            }
        }
        for(int i=0;i<n;i++) {
            if((nums[i] & lowBit)==0) {
                answer1^=nums[i];
            }else {
                answer2^=nums[i];
            }
        }
        
        return new int[]{answer1,answer2};
    }
}
```

```kotlin
// 位运算：两数缺失，整体异或分组求解（1..n 与 nums）
class Solution {
    fun missingTwo(nums: IntArray): IntArray {
        val n = nums.size + 2
        var xorAll = 0
        for (i in 1..n) xorAll = xorAll xor i
        for (v in nums) xorAll = xorAll xor v

        // xorAll = a xor b，取最低位的 1 作为分组标志位
        val lowbit = xorAll and (-xorAll)
        var a = 0
        var b = 0
        for (i in 1..n) {
            if ((i and lowbit) == 0) a = a xor i else b = b xor i
        }
        for (v in nums) {
            if ((v and lowbit) == 0) a = a xor v else b = b xor v
        }
        return intArrayOf(a, b)
    }
}
```

```kotlin
// Kotlin 函数式（FP）风格：用 fold/范围聚合，避免显式可变状态
class SolutionFp {
    fun missingTwo(nums: IntArray): IntArray {
        val n = nums.size + 2
        val xorAll = (1..n).fold(0) { acc, i -> acc xor i } xor
                     nums.fold(0) { acc, v -> acc xor v }
        val lowbit = xorAll and -xorAll

        val (a1, b1) = (1..n).fold(0 to 0) { (a, b), i ->
            if ((i and lowbit) == 0) (a xor i) to b else a to (b xor i)
        }
        val (a, b) = nums.fold(a1 to b1) { (a, b), v ->
            if ((v and lowbit) == 0) (a xor v) to b else a to (b xor v)
        }
        return intArrayOf(a, b)
    }
}
```

说明
- 思路：设缺失为 a、b。整体异或得到 `xorAll = a xor b`，按其最低位的 1 将 `1..n` 与 `nums` 分成两组，各组内部异或即可分别得到 a、b。
- 复杂度：时间 O(n)，空间 O(1)。
- Kotlin 特性：`xor/and` 位运算；范围 `1..n`；`intArrayOf` 构建结果数组。
- FP 说明：使用 `fold` 对范围与数组聚合，避免显式可变状态，逻辑等价更函数式。

