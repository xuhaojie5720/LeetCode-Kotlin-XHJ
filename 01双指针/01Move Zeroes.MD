# Move Zeroes（移动零）— Kotlin 学习样本

本页将同一算法的 Java 与 Kotlin 写法并列展示，帮助理解从 Java 迁移到 Kotlin 时的等价表达；算法与复杂度保持完全一致，不做任何逻辑改动。

## 原始 Java 代码

```java
class Solution {
    public void moveZeroes(int[] nums) {
        int n = nums.length;
        int leftHand = -1;

        for (int i = 0; i < n; i++) {
            if (nums[i] != 0) {
                leftHand++;
                int temp = nums[i];
                nums[i] = nums[leftHand];
                nums[leftHand] = temp;
            }
        }
    }
}
```

## 等价 Kotlin 代码（逻辑不变）

```kotlin
class Solution {
    fun moveZeroes(nums: IntArray) { // Kotlin：函数使用 fun；IntArray 对应 Java 的 int[]
        val n = nums.size // Kotlin：val（不可变）相当于 Java 的 final；长度用 size 属性
        var leftHand = -1 // Kotlin：var（可变）保存指针

        for (i in 0 until n) { // Kotlin：区间遍历 0 until n 等价于 i < n
            if (nums[i] != 0) {
                leftHand++
                val temp = nums[i] // Kotlin：临时变量优先使用 val
                nums[i] = nums[leftHand]
                nums[leftHand] = temp // Kotlin：原地交换与 Java 完全一致
            }
        }
    }
}
```

## Kotlin 要点对照

- val/var：`val` 不可变、`var` 可变，对应 Java 的 `final`/普通变量。
- 数组类型：`IntArray` 对应 Java 的 `int[]`；长度是 `size`，不是 `length` 字段。
- 循环写法：`for (i in 0 until n)` 更直观，`until` 为半开区间 [0, n)。
- 语句结尾：Kotlin 无需分号；同一逻辑的赋值与交换写法相同。

## 算法说明（与 Java 完全一致）

- 使用单指针 `leftHand` 指向「已处理的非零区间」的末尾位置。
- 遍历数组，当遇到非零元素时，将其与 `leftHand+1` 位置交换，并推进指针。
- 最终所有非零元素稳定移动到前部，零元素被挤压到末尾。

复杂度：时间 O(n)，空间 O(1)。

## 简单测试（Kotlin）

```kotlin
fun main() {
    val arr = intArrayOf(0, 1, 0, 3, 12)
    Solution().moveZeroes(arr)
    println(arr.joinToString(",")) // 期望：1,3,12,0,0
}
```

## Kotlin 函数式风格（等价逻辑）

```kotlin
// 使用 fold 聚合 leftHand 指针，避免外部可变状态；就地交换仍保持 O(1) 空间与 O(n) 时间
class SolutionFp {
    fun moveZeroes(nums: IntArray) {
        nums.indices.fold(-1) { left, i ->
            if (nums[i] == 0) left
            else {
                val v = nums[i]
                nums[i] = nums[left + 1].also { nums[left + 1] = v }
                left + 1
            }
        }
    }
}
```

说明（FP 版）
- 思路不变：单指针指向“已放好的非零尾部”，扫描到非零时与 `left+1` 交换。
- 函数式要点：用 `fold` 维护指针累积；无外部可变指针变量。
