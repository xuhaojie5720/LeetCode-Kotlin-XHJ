# Subarray Sum Equals K（和为 K 的子数组）— Kotlin 学习样本

本页并列展示 Java 与 Kotlin 写法，强调“前缀和 + 计数哈希”模板。保持逻辑与复杂度不变。

## 原始 Java 代码

```java
class Solution {
    public int subarraySum(int[] nums, int k) {
        Map<Integer, Integer> prefixSumCount = new HashMap<>();
        prefixSumCount.put(0, 1);
        int count = 0;
        int sum = 0;
        for (int num : nums) {
            sum += num;
            count += prefixSumCount.getOrDefault(sum - k, 0);
            prefixSumCount.merge(sum, 1, Integer::sum);
        }
        return count;
    }
}
```

## 等价 Kotlin 代码（逻辑不变）

```kotlin
class Solution {
    fun subarraySum(nums: IntArray, k: Int): Int {
        val freq = mutableMapOf<Int, Int>() // Kotlin：可变 Map
        freq[0] = 1 // 前缀和为 0 的计数（空前缀）
        var sum = 0
        var ans = 0
        for (x in nums) {
            sum += x
            ans += freq[sum - k] ?: 0 // getOrDefault 等价写法
            freq[sum] = (freq[sum] ?: 0) + 1 // 自增出现次数
        }
        return ans
    }
}
```

## Kotlin 要点对照

- Map 操作：`mutableMapOf`、下标读写、`?:` 提供默认值，替代 Java 的 `getOrDefault/merge`。
- 语法简洁：for-each 遍历 `nums`，累加与计数更直观。

## 算法说明

- 设前缀和 `sum[i]`，若存在 `j<i` 使得 `sum[i]-sum[j]==k`，则 `[j+1..i]` 为解。
- 用哈希表维护“某个前缀和出现次数”，每到新位置统计可配对的 `sum-k` 次数。
- 复杂度：时间 `O(n)`，空间 `O(n)`。

## 思路总结

- 将区间和转化为前缀和差；用计数表“一次遍历累积答案”。
- 初始化 `freq[0]=1` 用于包含从开头出发的子数组。

## 集合速览（Kotlin）

- 初始化：`mutableMapOf<Int, Int>()`、`hashMapOf(0 to 1)`。
- 读写：`m[key]` 读取，`m[key] = v` 写入；默认值可用 `m[key] ?: 0`。
- 常用：`for ((k, v) in m)` 遍历；`m.clear()` 清空；`m.containsKey(k)` 判断。

## 简单测试（Kotlin）

```kotlin
fun main() {
    println(Solution().subarraySum(intArrayOf(1,1,1), 2)) // 2
}
```

## Kotlin 函数式风格（等价逻辑）

```kotlin
// fold 聚合：携带 (sum, ans, 频次表) 累积答案
class SolutionFp {
    fun subarraySum(nums: IntArray, k: Int): Int {
        val acc = nums.fold(Triple(0, 0, mutableMapOf(0 to 1))) { (sum, ans, freq), x ->
            val s = sum + x
            val a = ans + (freq[s - k] ?: 0)
            freq[s] = (freq[s] ?: 0) + 1
            Triple(s, a, freq)
        }
        return acc.second
    }
}
```

说明（FP 版）
- 思路不变：前缀和差配对计数。
- 函数式要点：单次 fold 完成累积与统计，保留 O(n) 时间。
