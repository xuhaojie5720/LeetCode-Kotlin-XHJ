# Contiguous Array（连续数组）— Kotlin 学习样本

本页并列展示 Java 与 Kotlin 写法，强调“将 0 视作 -1 的前缀和 + 首次出现索引”模板。

## 原始 Java 代码

```java
class Solution {
    public int findMaxLength(int[] nums) {
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] == 0) nums[i] = -1;
        }
        Map<Integer, Integer> prefixSumIndex = new HashMap<>();
        prefixSumIndex.put(0, -1);
        int maxLength = 0, sum = 0;
        for (int i = 0; i < nums.length; i++) {
            sum += nums[i];
            if (prefixSumIndex.containsKey(sum)) {
                maxLength = Math.max(maxLength, i - prefixSumIndex.get(sum));
            } else {
                prefixSumIndex.put(sum, i);
            }
        }
        return maxLength;
    }
}
```

## 等价 Kotlin 代码（逻辑不变）

```kotlin
class Solution {
    fun findMaxLength(nums: IntArray): Int {
        // 将 0 视作 -1，使“0/1 数量相等”转化为“前缀和相同”的最长区间
        for (i in nums.indices) if (nums[i] == 0) nums[i] = -1

        val first = mutableMapOf<Int, Int>() // 记录某前缀和首次出现的索引
        first[0] = -1 // 前缀和为 0 在索引 -1 处
        var sum = 0
        var best = 0
        for (i in nums.indices) {
            sum += nums[i]
            val j = first[sum]
            if (j != null) {
                best = maxOf(best, i - j)
            } else {
                first[sum] = i
            }
        }
        return best
    }
}
```

## Kotlin 要点对照

- `indices` 安全遍历下标；`maxOf` 替代 `Math.max`。
- Map：`mutableMapOf` + 首次出现索引表，避免覆盖更早位置。

## 算法说明

- 将 0 替换为 -1 后，问题转化为“最长前缀和相等的区间长度”。
- 维护每个前缀和的首次出现位置，后续再次遇到即可更新长度。
- 复杂度：时间 `O(n)`，空间 `O(n)`。

## 思路总结

- “平衡 0/1”⇔“前缀和相等”，首位表 + 单次遍历即可。
- 初始化 `first[0] = -1` 覆盖从头开始的区间。

## 集合速览（Kotlin）

- 初始化：`mutableMapOf<Int, Int>(0 to -1)`。
- 查询：`m[key]` 返回可空值；空合并 `?:` 提供默认。
- 更新：只在首次遇到前缀和时 `put`，以保留最左位置。

## 简单测试（Kotlin）

```kotlin
fun main() {
    println(Solution().findMaxLength(intArrayOf(0,1)))         // 2
    println(Solution().findMaxLength(intArrayOf(0,1,0)))       // 2
}
```
