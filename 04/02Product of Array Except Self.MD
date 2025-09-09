# Product of Array Except Self（除自身以外数组的乘积）— Kotlin 学习样本

本页并列展示 Java 与 Kotlin 实现；保持“前缀积 + 后缀积”思路与复杂度不变。

## 原始 Java 代码

```java
class Solution {
    public int[] productExceptSelf(int[] nums) {
        int n = nums.length;
        int[] answer_prev = new int[n];
        int[] answer_post = new int[n];

        answer_prev[0] = 1;
        answer_post[n - 1] = 1;

        for (int i = 1; i < n; i++) {
            answer_prev[i] = answer_prev[i - 1] * nums[i - 1];
            answer_post[n - 1 - i] = answer_post[n - i] * nums[n - i];
        }

        int[] answer = new int[n];
        for (int i = 0; i < n; i++) {
            answer[i] = answer_prev[i] * answer_post[i];
        }
        return answer;
    }
}
```

## 等价 Kotlin 代码（逻辑不变）

```kotlin
class Solution {
    fun productExceptSelf(nums: IntArray): IntArray {
        val n = nums.size
        val pre = IntArray(n)
        val suf = IntArray(n)
        pre[0] = 1
        suf[n - 1] = 1

        for (i in 1 until n) {
            pre[i] = pre[i - 1] * nums[i - 1]
            suf[n - 1 - i] = suf[n - i] * nums[n - i]
        }

        val ans = IntArray(n)
        for (i in 0 until n) {
            ans[i] = pre[i] * suf[i]
        }
        return ans
    }
}
```

## Kotlin 要点对照

- 数组构造：`IntArray(n)` 初始化；索引与 Java 等价。
- 区间遍历：`until` 半开区间；避免 `i < n` 手写。
- 空间与时间：与 Java 相同 `O(n)`，未引入多余拷贝。

## 算法说明

- 核心：`pre[i]` 为左侧积，`suf[i]` 为右侧积；答案等于二者乘积。
- 复杂度：时间 `O(n)`，空间 `O(n)`；可选优化为单数组两趟（此处按原逻辑保持不变）。

## 思路总结

- 将“除自身”转化为“左积 × 右积”，避开除法与零处理。
- 计算顺序相反、覆盖互补，边界设置为 1。

## 简单测试（Kotlin）

```kotlin
fun main() {
    println(Solution().productExceptSelf(intArrayOf(1,2,3,4)).joinToString()) // 24,12,8,6
}
```
