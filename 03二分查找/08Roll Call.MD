# Roll Call（点名）与二分模板速览 — Kotlin 学习样本

本页包含一个典型的“点名缺号定位”问题（本质是查找第一个不匹配位置），并给出二分模板速览，便于统一迁移与复用。

## 原始 Java 代码（点名）

```java
class Solution {
    public int takeAttendance(int[] records) {
        int n = records.length;
        if (records[n - 1] == n - 1) return n; // 连续则缺号为 n
        int left = 0, right = n - 1;
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (mid == records[mid]) left = mid + 1; // 左侧连续，缺号在右半
            else right = mid; // 断点在左半（含 mid）
        }
        return left; // 第一个下标 != 值 的位置
    }
}
```

## 等价 Kotlin 代码（逻辑不变）

```kotlin
class Solution {
    fun takeAttendance(records: IntArray): Int {
        val n = records.size
        if (records[n - 1] == n - 1) return n
        var l = 0
        var r = n - 1
        while (l < r) {
            val m = l + (r - l) / 2
            if (m == records[m]) l = m + 1 else r = m
        }
        return l
    }
}
```

## 二分查找模板速览（Kotlin）

```kotlin
// 1) 经典闭区间 [l, r]：精确查找/判定
fun binarySearch(nums: IntArray, target: Int): Int {
    var l = 0
    var r = nums.lastIndex
    while (l <= r) {
        val m = l + (r - l) / 2
        when {
            nums[m] == target -> return m
            nums[m] < target -> l = m + 1
            else -> r = m - 1
        }
    }
    return -1
}

// 2) 下界 lowerBound：第一个 >= x（半开 [l, r)）
fun lowerBound(a: IntArray, x: Int): Int {
    var l = 0
    var r = a.size
    while (l < r) {
        val m = l + (r - l) / 2
        if (a[m] >= x) r = m else l = m + 1
    }
    return l
}

// 3) 上界 upperBound：第一个 > x（半开 [l, r)）
fun upperBound(a: IntArray, x: Int): Int {
    var l = 0
    var r = a.size
    while (l < r) {
        val m = l + (r - l) / 2
        if (a[m] > x) r = m else l = m + 1
    }
    return l
}

// 4) 单峰数组找峰（爬坡）
fun peakIndex(arr: IntArray): Int {
    var l = 0
    var r = arr.lastIndex
    while (l < r) {
        val m = l + (r - l) / 2
        if (arr[m] < arr[m + 1]) l = m + 1 else r = m
    }
    return l
}

// 5) 旋转数组最小值
fun findMinRotated(nums: IntArray): Int {
    var l = 0
    var r = nums.lastIndex
    while (l < r) {
        val m = l + (r - l) / 2
        if (nums[m] <= nums[r]) r = m else l = m + 1
    }
    return nums[l]
}
```

## 要点与建议

- 模板选择：
  - 闭区间适合“是否存在”“返回下标”类问题。
  - 下/上界适合“范围界定/插入位置/计数”类问题。
- Kotlin 习惯：`IntArray`、`lastIndex`、`val/var`、`when` 提升可读性；逻辑与复杂度不变。

## 思路总结（点名题）

- 问题抽象：在下标与值都递增的数组中，寻找第一个位置 `i` 使得 `records[i] != i`。
- 单调谓词：定义 `P(i): records[i] == i`，其随 i 非增（先真后假），适合找“第一个为假”。
- 区间设计：`[l, r]` 收缩为单点；若 `P(m)` 为真，缺号在右侧；否则在左侧含 m。
- 末尾特判：若 `records[n-1] == n-1`，说明 0..n-1 均匹配，缺号为 n。
- 正确性：`P(i)` 的单调性保证了“第一个断点”唯一，二分能在对数时间内定位。

## Kotlin 函数式风格（等价逻辑）

```kotlin
// 用尾递归实现“第一个不匹配位置”的查找
class SolutionFp {
    fun takeAttendance(records: IntArray): Int {
        val n = records.size
        if (records[n - 1] == n - 1) return n
        tailrec fun go(l: Int, r: Int): Int {
            if (l >= r) return l
            val m = l + (r - l) / 2
            return if (m == records[m]) go(m + 1, r) else go(l, m)
        }
        return go(0, n - 1)
    }
}
```

说明（FP 版）
- 思路不变：谓词 `P(i): records[i]==i` 单调，找第一个为假的下标。
- 函数式要点：尾递归携带区间，不使用显式 while。
