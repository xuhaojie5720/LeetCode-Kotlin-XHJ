# Happy Number（快乐数）— Kotlin 学习样本

本页展示快乐数判定（Floyd 判圈）在 Java 与 Kotlin 下的等价实现；逻辑与复杂度保持完全一致。

## 原始 Java 代码

```java
class Solution {
    private int sumOfSquares(int num){
        int sum=0;
        while(num!=0){
            sum+=(int)Math.pow((num%10),2);
            num/=10;
        }
        return sum;
    }

    public boolean isHappy(int n) {
        int slowPtr=sumOfSquares(n);
        int fastPtr=sumOfSquares(sumOfSquares(n));
        while(slowPtr!=fastPtr){
            slowPtr=sumOfSquares(slowPtr);
            fastPtr=sumOfSquares(sumOfSquares(fastPtr));
        }
        return slowPtr==1;
    }
}
```

## 等价 Kotlin 代码（逻辑不变）

```kotlin
class Solution {
    private fun sumOfSquares(num: Int): Int { // Kotlin：函数用 fun；返回类型在冒号后标注
        var x = num // Kotlin：形参不可变，拷贝到局部 var 以便修改
        var sum = 0
        while (x != 0) {
            val d = x % 10 // Kotlin：val（不可变）等价于 Java 的 final
            sum += d * d // Kotlin：直接整数乘法，等价于 Java 的 (int)Math.pow(d,2)
            x /= 10
        }
        return sum
    }

    fun isHappy(n: Int): Boolean { // Kotlin：Boolean 返回值相同；可见性默认 public
        var slowPtr = sumOfSquares(n)
        var fastPtr = sumOfSquares(sumOfSquares(n))
        while (slowPtr != fastPtr) {
            slowPtr = sumOfSquares(slowPtr)
            fastPtr = sumOfSquares(sumOfSquares(fastPtr))
        }
        return slowPtr == 1
    }
}
```

## Kotlin 要点对照

- 函数声明：`fun name(args): ReturnType`，返回类型在冒号右侧。
- 不可变/可变：`val` 不可变、`var` 可变；函数参数默认不可变，需复制到 `var` 才能修改。
- 数学运算：整数平方可直接 `d * d`，避免装箱与 `Math.pow` 的双精度转换。
- 可见性：类内未显式标注时默认为 `public`，与 Java 一致对外可见。

## 简单测试（Kotlin）

```kotlin
fun main() {
    val s = Solution()
    println(s.isHappy(19)) // true
    println(s.isHappy(2))  // false
}
```

## Kotlin 函数式风格（等价逻辑：Floyd 判圈）

```kotlin
// 使用 tailrec + generateSequence 表达平方和与快慢指针推进
class SolutionFp {
    private fun sumOfSquares(num: Int): Int =
        generateSequence(num) { if (it == 0) null else it / 10 }
            .map { it % 10 }
            .sumOf { it * it }

    private tailrec fun advance(slow: Int, fast: Int): Boolean =
        if (slow == fast) slow == 1
        else advance(sumOfSquares(slow), sumOfSquares(sumOfSquares(fast)))

    fun isHappy(n: Int): Boolean = advance(sumOfSquares(n), sumOfSquares(sumOfSquares(n)))
}
```

说明（FP 版）
- 思路不变：Floyd 判圈；`sumOfSquares` 用 `generateSequence` 构造位流后求和。
- 函数式要点：尾递归推进快慢指针；函数以表达式为主，减少可变状态。
