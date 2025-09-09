# Valid Triangle Number（三角形的个数）—Kotlin 学习样本

本页并列展示 Java 与 Kotlin 的等价写法，逻辑与复杂度保持完全一致。

## 原始 Java 代码

```java
import java.util.Arrays;

class Solution {
    public int triangleNumber(int[] nums) {
        if (nums.length < 3) return 0;
        Arrays.sort(nums);
        int result = 0;
        for (int c = nums.length - 1; c >= 2; c--) {
            int a = 0, b = c - 1;
            while (a < b) {
                if (nums[a] + nums[b] > nums[c]) {
                    result += (b - a);
                    b--;
                } else {
                    a++;
                }
            }
        }
        return result;
    }
}
```

## 等价 Kotlin 代码（逻辑不变）

```kotlin
class Solution {
    fun triangleNumber(nums: IntArray): Int {
        if (nums.size < 3) return 0 // Kotlin：提前返回

        nums.sort() // Kotlin：IntArray 原地排序 O(n log n)

        var result = 0
        var c = nums.lastIndex // Kotlin：lastIndex 代替 size - 1

        while (c >= 2) { // 固定最大边索引 c
            var a = 0
            var b = c - 1 // 双指针夹逼
            while (b > a) {
                if (nums[a] + nums[b] > nums[c]) {
                    result += (b - a) // 满足三角不等式时，a..b-1 皆可
                    b--
                } else {
                    a++
                }
            }
            c--
        }
        return result
    }
}
```

## Kotlin 要点对照

- 原地排序：`IntArray.sort()`，时间 O(n log n)，空间 O(1)。
- 末尾索引：`lastIndex` 语义更清晰，避免手写 `size-1`。
- 可变/不可变：索引与计数使用 `var`，常量使用 `val`。
- 控制流：`while` 指针法与 Java 等价，不引入额外集合操作以保持 O(n^2)。
- 空安全：未使用可空类型，避免不必要的 `?` 或 `!!`。

## 算法说明（与 Java 完全一致）

- 排序后固定最大边 c，双指针 a、b 在 [0, c-1] 线性移动统计可行对数。
- 当 `nums[a] + nums[b] > nums[c]` 时，`a..b-1` 全满足，可一次性累加；否则左指针右移。
- 时间 O(n^2)，空间 O(1)。

## 简单测试（Kotlin）

```kotlin
fun main() {
    val nums = intArrayOf(2,2,3,4)
    println(Solution().triangleNumber(nums)) // 3
}
```

## Kotlin 函数式风格（等价逻辑）

```kotlin
// 用尾递归表达外层 c 与内层双指针 a/b 的推进
class SolutionFp {
    fun triangleNumber(nums: IntArray): Int {
        if (nums.size < 3) return 0
        nums.sort()
        tailrec fun countFor(c: Int, total: Int): Int {
            if (c < 2) return total
            tailrec fun countAB(a: Int, b: Int, acc: Int): Int =
                if (a >= b) acc
                else if (nums[a] + nums[b] > nums[c]) countAB(a, b - 1, acc + (b - a))
                else countAB(a + 1, b, acc)
            return countFor(c - 1, total + countAB(0, c - 1, 0))
        }
        return countFor(nums.lastIndex, 0)
    }
}
```

说明（FP 版）
- 思路不变：排序 + 外层固定最大边 + 内层双指针线性统计。
- 函数式要点：递归参数携带累积答案，去除显式 while。
