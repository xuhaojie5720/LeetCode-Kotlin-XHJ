# Binary Search（二分查找）— Kotlin 学习样本

本页将同一算法的 Java 与 Kotlin 写法并列展示，帮助理解从 Java 迁移到 Kotlin 时的等价表达；算法与复杂度保持完全一致，不做任何逻辑改动。

## 原始 Java 代码

```java
class Solution {
    public int search(int[] nums, int target) {
        int n = nums.length;
        int left = 0;
        int right = n - 1;

        while (left <= right) {
            int mid = left + (right - left) / 2; // 防溢出写法
            if (nums[mid] == target) return mid;
            else if (nums[mid] < target) left = mid + 1;
            else right = mid - 1;
        }

        return -1;
    }
}
```

## 等价 Kotlin 代码（逻辑不变）

```kotlin
class Solution {
    fun search(nums: IntArray, target: Int): Int { // 使用 IntArray（Kotlin 原生整型数组）
        var left = 0
        var right = nums.lastIndex // Kotlin: lastIndex 属性
        while (left <= right) {
            val mid = left + (right - left) / 2 // val：不可变临时变量
            when { // Kotlin 的 when 表达式，比多重 if 更清晰
                nums[mid] == target -> return mid
                nums[mid] < target -> left = mid + 1
                else -> right = mid - 1
            }
        }
        return -1
    }
}
```

## Kotlin 要点对照

- 数组类型：`IntArray` 对应 Java 的 `int[]`；末尾下标用 `lastIndex`。
- 分支表达：`when` 结构替代多重 `if-else`，更紧凑。
- 不可变/可变：`val` 与 `var` 区分临时值与指针。

## 算法说明（与 Java 等价）

- 闭区间模板 `[left, right]`，循环条件 `left <= right`。
- 每次比较后排除一半区间，时间 `O(log n)`，空间 `O(1)`。
- 中点写法 `left + (right - left) / 2` 避免整型溢出。

## 思路总结

- 模板选择：闭区间二分，返回“是否存在/确切下标”。
- 循环不变式：目标若存在，始终落在区间 `[left, right]` 内。
- 收敛方式：命中返回；否则排除 `mid` 一侧，区间长度单调缩小。
- 边界处理：空数组直接不进入循环；单元素时仍满足模板。
- 正确性：有界单调收缩 + 不变式维持，终将命中或区间耗尽返回 -1。

## 简单测试（Kotlin）

```kotlin
fun main() {
    val nums = intArrayOf(-1,0,3,5,9,12)
    println(Solution().search(nums, 9))  // 4
    println(Solution().search(nums, 2))  // -1
}
```

## Kotlin 函数式风格（等价逻辑）

```kotlin
// 用尾递归实现闭区间二分；保持 O(log n)/O(1)
class SolutionFp {
    fun search(nums: IntArray, target: Int): Int {
        tailrec fun bs(l: Int, r: Int): Int {
            if (l > r) return -1
            val m = l + (r - l) / 2
            return when {
                nums[m] == target -> m
                nums[m] < target -> bs(m + 1, r)
                else -> bs(l, m - 1)
            }
        }
        return bs(0, nums.lastIndex)
    }
}
```

说明（FP 版）
- 思路不变：闭区间模板；尾递归代替 while。
- 函数式要点：状态经参数传递；返回命中下标或 -1。
