# Subarray Sums Divisible by K（和可被 K 整除的子数组）— Kotlin 学习样本

本页并列展示 Java 与 Kotlin 写法，强调“前缀和模 K 计数”模板。保持逻辑与复杂度不变。

## 原始 Java 代码

```java
class Solution {
    public int subarraysDivByK(int[] nums, int k) {
        int n = nums.length;
        Map<Integer, Integer> map = new HashMap<>();
        map.put(0, 1);
        int sum = 0, ret = 0;
        for (int i = 0; i < n; i++) {
            sum += nums[i];
            int r = (sum % k + k) % k; // 归一化为 [0, k)
            ret += map.getOrDefault(r, 0);
            map.merge(r, 1, Integer::sum);
        }
        return ret;
    }
}
```

## 等价 Kotlin 代码（逻辑不变）

```kotlin
class Solution {
    fun subarraysDivByK(nums: IntArray, k: Int): Int {
        val freq = mutableMapOf<Int, Int>() // 记录每个余数出现次数
        freq[0] = 1
        var sum = 0
        var ans = 0
        for (x in nums) {
            sum += x
            val r = ((sum % k) + k) % k // 处理负数，确保落在 [0, k)
            ans += freq[r] ?: 0
            freq[r] = (freq[r] ?: 0) + 1
        }
        return ans
    }
}
```

## Kotlin 要点对照

- Map：`mutableMapOf` + 下标操作 + Elvis `?:` 提供默认值。
- 余数归一：`((sum % k) + k) % k` 处理负数。

## 算法说明

- 若 `sum[i] % k == sum[j] % k`，则 `[j+1..i]` 的和能被 `k` 整除。
- 统计每个余数的出现次数，累加形成答案；时间 `O(n)`，空间 `O(k)`（上界 `O(n)`）。

## 思路总结

- 将“可整除”转化为“相同余数”的配对计数。
- 初始化 `freq[0]=1` 代表从开头取的子数组。

## 集合速览（Kotlin）

- 初始化：`mutableMapOf<Int, Int>()`、`hashMapOf(0 to 1)`。
- 自增：`m[key] = (m[key] ?: 0) + 1`。
- 遍历：`for ((k, v) in m)`。

## 简单测试（Kotlin）

```kotlin
fun main() {
    println(Solution().subarraysDivByK(intArrayOf(4,5,0,-2,-3,1), 5)) // 7
}
```
