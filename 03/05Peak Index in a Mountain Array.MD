# Peak Index in a Mountain Array（山脉数组的峰顶索引）— Kotlin 学习样本

本页展示“爬坡型二分”模板：比较 `arr[m]` 与 `arr[m+1]`，确定峰值所在方向。

## 原始 Java 代码

```java
class Solution {
    public int peakIndexInMountainArray(int[] arr) {
        int n = arr.length;
        int leftHand = 0, rightHand = n - 1;
        while (leftHand <= rightHand) {
            int mid = leftHand + (rightHand - leftHand) / 2;
            if (mid == 0) leftHand = mid + 1;
            else if (mid == n - 1) rightHand = mid - 1;
            else if (arr[mid] > arr[mid - 1] && arr[mid] > arr[mid + 1]) return mid;
            else if (arr[mid] > arr[mid - 1] && arr[mid] < arr[mid + 1]) leftHand = mid + 1;
            else if (arr[mid] < arr[mid - 1] && arr[mid] > arr[mid + 1]) rightHand = mid - 1;
        }
        return -1;
    }
}
```

## 等价 Kotlin 代码（逻辑不变）

```kotlin
class Solution {
    fun peakIndexInMountainArray(arr: IntArray): Int {
        var l = 0
        var r = arr.lastIndex // Kotlin: lastIndex
        // 单峰数组：若 arr[m] < arr[m+1]，峰在右侧；否则在左侧（含 m）
        while (l < r) { // 收缩到单点
            val m = l + (r - l) / 2
            if (arr[m] < arr[m + 1]) l = m + 1 else r = m
        }
        return l // 或 r，二者相等
    }
}
```

## Kotlin 要点对照

- `lastIndex` 获取末尾下标；`val/var` 区分临时与指针。
- 用趋势判断方向，无需额外边界分支。

## 算法说明

- 若 `arr[m] < arr[m+1]`，峰在右侧；否则在左侧（含 m）。
- 区间收缩到单点即为答案；时间 `O(log n)`，空间 `O(1)`。

## 思路总结

- 山脉性质：先增后减，峰值唯一；比较 `arr[m]` 与 `arr[m+1]` 就能判定峰在何侧。
- 收敛策略：若右侧更大，则 `l=m+1`；否则 `r=m`，保证峰始终在 `[l, r]`。
- 边界安全：循环条件 `l<r` 确保访问 `m+1` 安全；最终 `l==r` 为峰。
- 替代写法：相比显式比较两侧邻居，爬坡法更简洁，无需单独处理 `m==0/n-1`。
- 正确性：单峰数组“梯度”指向峰顶，单调收缩下必达峰值位置。

## 简单测试（Kotlin）

```kotlin
fun main() {
    val arr = intArrayOf(0,2,5,3,1)
    println(Solution().peakIndexInMountainArray(arr)) // 2
}
```
