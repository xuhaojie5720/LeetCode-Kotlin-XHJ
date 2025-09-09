# Sqrt(x)（x 的平方根）— Kotlin 学习样本

展示二分查找计算整数平方根的 Java 与 Kotlin 等价实现。两种常见防溢出策略：Java 用 `x / mid`，Kotlin 用 Long。

## 原始 Java 代码

```java
class Solution {
    public int mySqrt(int x) {
        if (x == 0 || x == 1) return x;
        int left = 1, right = x;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (mid > x / mid) right = mid - 1; // 通过除法避免 mid*mid 溢出
            else left = mid + 1;
        }
        return right; // right 为满足 mid*mid <= x 的最大 mid
    }
}
```

## 等价 Kotlin 代码（逻辑不变）

```kotlin
class Solution {
    fun mySqrt(x: Int): Int {
        // 使用 Long 防止 mid*mid 溢出（Kotlin/Java 中 int 可能溢出）
        var l = 0L
        var r = x.toLong()
        var ans = 0L // 记录满足 mid*mid <= x 的最大 mid
        while (l <= r) {
            val m = l + (r - l) / 2
            if (m * m <= x) { // m 为 Long，但与 Int 比较会自动提升
                ans = m
                l = m + 1
            } else {
                r = m - 1
            }
        }
        return ans.toInt()
    }
}
```

## Kotlin 要点对照

- 溢出处理：改用 `Long` 进行乘法比较，结束后 `toInt()` 回转。
- 闭区间模板：`[l, r]` + `l <= r`，用 `ans` 保存最大可行解。
- 转换函数：`x.toLong()`、`ans.toInt()` 显式类型转换。

## 算法说明

- 目标：最大整数 `ans` 使得 `ans*ans <= x`。
- 时间 `O(log x)`，空间 `O(1)`。

## 思路总结

- 单调性：函数 `f(m)=m*m` 随 m 单调递增；寻找满足 `m*m <= x` 的最大 m。
- 谓词设计：用 `m*m <= x` 判断可行与否；若溢出风险，用 Long 或 Java 的 `x / m`。
- 区间与答案：闭区间二分并用 `ans` 记录可行解，向上尝试不可行则回退。
- 边界：`x` 为 0/1 时直接返回；其他情况模板统一处理。
- 正确性：可行集合为前缀区间，二分寻找前缀最大点是标准用法。

## 简单测试（Kotlin）

```kotlin
fun main() {
    println(Solution().mySqrt(4))   // 2
    println(Solution().mySqrt(8))   // 2
    println(Solution().mySqrt(2147395599)) // 46339
}
```
