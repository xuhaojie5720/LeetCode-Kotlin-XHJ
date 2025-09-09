# Two Sum（两数之和，已排序数组版）—Kotlin 学习样本

本页并列展示同一算法的 Java 与 Kotlin 写法，逻辑与复杂度保持完全一致，便于从 Java 迁移到 Kotlin。

> 说明：当前实现基于“价格数组已按非降序排序”的假设，返回满足条件的两个商品价格值（非索引）。

## 原始 Java 代码

```java
class Solution {
    public int[] twoSum(int[] price, int target) {
        if (price.length < 2) return new int[0];

        int leftHand = 0;
        int rightHand = price.length - 1;
        while (leftHand < rightHand) {
            int sum = price[leftHand] + price[rightHand];
            if (sum == target) {
                return new int[]{ price[leftHand], price[rightHand] };
            } else if (sum < target) {
                leftHand++;
            } else {
                rightHand--;
            }
        }
        return new int[0];
    }
}
```

## 等价 Kotlin 代码（逻辑不变）

```kotlin
class Solution {
    fun twoSum(price: IntArray, target: Int): IntArray {
        if (price.size < 2) return intArrayOf() // Kotlin：空数组返回

        var l = 0
        var r = price.lastIndex // Kotlin：lastIndex 代替 length-1
        while (l < r) {
            val sum = price[l] + price[r]
            when {
                sum == target -> return intArrayOf(price[l], price[r]) // Kotlin：直接构造返回值
                sum < target -> l++
                else -> r--
            }
        }
        return intArrayOf()
    }
}
```

## Kotlin 要点对照

- 数组与索引：`IntArray` 对应 Java 的 `int[]`；使用 `lastIndex` 表达末尾索引更直观。
- 分支表达：`when` 替代多层 if-else，提高可读性，无额外开销。
- 返回数组：使用 `intArrayOf(a, b)`；空结果 `intArrayOf()` 等价 `new int[0]`。
- 排序假设：与原 Java 一致，默认输入已按非降序排序；若未排序需先排序会改变返回的元素集合含义（保持当前不改动）。

## 算法说明（与 Java 完全一致）

- 双指针从两端向中间靠拢：若和小于目标，左指针右移；若和大于目标，右指针左移；相等则返回。
- 时间复杂度 O(n)，空间复杂度 O(1)。

## 简单测试（Kotlin）

```kotlin
fun main() {
    val price = intArrayOf(2, 3, 7, 11, 15) // 已排序
    println(Solution().twoSum(price, 9).joinToString(",")) // 2,7
}
```

## Kotlin 函数式风格（等价逻辑）

```kotlin
// 用尾递归表达双指针推进；保持 O(1) 空间
class SolutionFp {
    fun twoSum(price: IntArray, target: Int): IntArray {
        if (price.size < 2) return intArrayOf()
        tailrec fun go(l: Int, r: Int): IntArray {
            if (l >= r) return intArrayOf()
            val s = price[l] + price[r]
            return when {
                s == target -> intArrayOf(price[l], price[r])
                s < target -> go(l + 1, r)
                else -> go(l, r - 1)
            }
        }
        return go(0, price.lastIndex)
    }
}
```

说明（FP 版）
- 思路不变：有序数组的双指针夹逼。
- 函数式要点：`tailrec` 消除 while；递归参数承载窗口位置。
