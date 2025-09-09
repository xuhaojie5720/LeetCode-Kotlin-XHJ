# Container With Most Water（盛最多水的容器）—Kotlin 学习样本

本页并列展示同一算法的 Java 与 Kotlin 写法，逻辑与复杂度保持完全一致，便于对照学习 Kotlin 表达方式。

## 原始 Java 代码

```java
class Solution {
    public int maxArea(int[] height) {
        int left = 0;
        int right = height.length - 1;
        int capacity = Math.min(height[left], height[right]) * (right - left);

        while (left < right) {
            int minHeightIndex = height[left] > height[right] ? right : left;

            if (minHeightIndex == left) {
                while (left < right && height[left] <= height[minHeightIndex]) {
                    left += 1;
                }
            } else {
                while (left < right && height[right] <= height[minHeightIndex]) {
                    right -= 1;
                }
            }

            int newCapacity = Math.min(height[left], height[right]) * (right - left);
            capacity = Math.max(capacity, newCapacity);
        }

        return capacity;
    }
}
```

## 等价 Kotlin 代码（逻辑不变）

```kotlin
import kotlin.math.max
import kotlin.math.min

class Solution {
    fun maxArea(height: IntArray): Int {
        var l = 0
        var r = height.lastIndex // Kotlin：用 lastIndex 代替 length - 1
        var ans = 0
        while (l < r) {
            val h = min(height[l], height[r]) // Kotlin：kotlin.math.min
            ans = max(ans, h * (r - l))       // Kotlin：kotlin.math.max
            if (height[l] < height[r]) l++ else r--
        }
        return ans
    }
}
```

## Kotlin 要点对照

- 数组类型：`IntArray` 对应 Java 的 `int[]`；长度用 `size`，末尾索引用 `lastIndex`。
- 数学函数：使用 `kotlin.math.min/max`，避免 `java.lang.Math` 静态导入。
- 指针迭代：`while (l < r)` 与 Java 等价，指针自增自减同样适用。
- 不可变/可变：`val` 不可变、`var` 可变；指针需变化故使用 `var`。

## 算法说明（与 Java 完全一致）

- 双指针从两端向中间收缩，每次以较小高度为瓶颈计算面积并移动对应指针。
- 单调性保证了不会错过最优解，时间 O(n)，空间 O(1)。

## 简单测试（Kotlin）

```kotlin
fun main() {
    val h = intArrayOf(1,8,6,2,5,4,8,3,7)
    println(Solution().maxArea(h)) // 49
}
```

## Kotlin 函数式风格（等价逻辑）

```kotlin
// 用尾递归表示双指针收缩，保持 O(1) 空间与 O(n) 时间
class SolutionFp {
    fun maxArea(height: IntArray): Int {
        tailrec fun go(l: Int, r: Int, best: Int): Int {
            if (l >= r) return best
            val h = kotlin.math.min(height[l], height[r])
            val area = kotlin.math.max(best, h * (r - l))
            return if (height[l] < height[r]) go(l + 1, r, area) else go(l, r - 1, area)
        }
        return go(0, height.lastIndex, 0)
    }
}
```

说明（FP 版）
- 思路不变：每轮以短板高度计面积并移动对应指针。
- 函数式要点：`tailrec` 递归代替 while，状态通过参数传递。
