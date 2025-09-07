# Duplicate Zeros（重复零）— 更易读实现（逻辑不变）

下面首先保留原始实现，随后给出等价但更易读的 Java 版本，并在关键处添加注释。算法与边界处理保持完全一致。

## 原始 Java 代码

```java
class Solution {
    public void duplicateZeros(int[] arr) {
        int targetEndIndex=-1,count=0;
        while(count<arr.length){
            targetEndIndex+=1;
            if(arr[targetEndIndex]==0){
                count+=2;
            }else{
                count+=1;
            }
        }

        int cur=arr.length-1;
        if(count==arr.length+1){
            arr[arr.length-1]=0;
            targetEndIndex-=1;
            cur-=1;
        }

        for(;targetEndIndex>=0;targetEndIndex-=1){
            if(arr[targetEndIndex]==0){
                for(int i=0;i<2;i++){
                    arr[cur]=0;
                    cur-=1;
                }
            }else{
                arr[cur]=arr[targetEndIndex];
                cur-=1;
            }
        }
    }
}
```

## 更加便于阅读的 Java 写法（逻辑完全一致）

```java
class Solution {
    public void duplicateZeros(int[] arr) {
        int n = arr.length;

        // 第一步：找到“可写窗口”的末尾 writeEnd，使得把 0 计为两格后总长达到或刚超过 n
        int writeEnd = -1;
        int filled = 0; // 已“占用”的长度（0 计 2，非 0 计 1）
        while (filled < n) {
            writeEnd++;
            filled += (arr[writeEnd] == 0) ? 2 : 1;
        }

        // 第二步：从右向左回填。
        // 若 filled == n + 1，说明最后一个 0 只能“复制一半”（只写一个 0 在末尾）
        int i = n - 1; // 写指针
        if (filled == n + 1) {
            arr[i] = 0; // 只能写下一个 0
            writeEnd--; // 收缩一位，避免再次处理这个 0
            i--;        // 写指针左移
        }

        // 常规回填：遇到 0 写两次 0，遇到非 0 写一次该数
        for (; writeEnd >= 0; writeEnd--) {
            if (arr[writeEnd] == 0) {
                arr[i--] = 0;
                arr[i--] = 0;
            } else {
                arr[i--] = arr[writeEnd];
            }
        }
    }
}
```

## 说明

- writeEnd/filled 命名更清晰，表达“扩展后长度”的计算过程。
- 统一使用 `i--` 回填，去掉内部二重循环，使流程更直观。
- 与原代码的边界判定一致：仅当 `filled == n + 1` 时写入单个 0 并回退 writeEnd。

## 等价 Kotlin 代码（逻辑不变）

```kotlin
class Solution {
    fun duplicateZeros(arr: IntArray) { // Kotlin：函数用 fun；IntArray 对应 Java 的 int[]
        val n = arr.size // Kotlin：val（不可变）相当于 Java 的 final；数组长度用 size 属性

        // 第一步：计算扩展后长度与可写末尾
        var writeEnd = -1 // Kotlin：var（可变）
        var filled = 0
        while (filled < n) {
            writeEnd++
            filled += if (arr[writeEnd] == 0) 2 else 1 // Kotlin：if 是表达式，直接返回值
        }

        // 第二步：从右向左回填
        var i = n - 1 // 写指针
        if (filled == n + 1) {
            arr[i] = 0
            writeEnd--
            i--
        }

        val end = writeEnd // Kotlin：使用 val 固定本轮的上界
        for (idx in end downTo 0) { // Kotlin：downTo 生成递减区间，更直观
            if (arr[idx] == 0) {
                arr[i--] = 0 // Kotlin：与 Java 相同的 i-- 写法
                arr[i--] = 0
            } else {
                arr[i--] = arr[idx]
            }
        }
    }
}
```

### Kotlin 要点对照

- 不可变/可变：`val` 不可变、`var` 可变，对应 Java 的 `final`/普通变量。
- 数组类型：`IntArray` 对应 Java 的 `int[]`；长度为 `size` 而非 `length` 字段。
- 表达式风格：`if` 可作为表达式直接产生值（如 `filled += if (...) 2 else 1`）。
- 区间遍历：`for (i in a downTo b)` 生成递减序列，可读性更好。
- 语句结尾：Kotlin 无需分号；其余赋值与原地操作与 Java 等价。

### 简单测试（Kotlin）

```kotlin
fun main() {
    val arr = intArrayOf(1,0,2,3,0,4,5,0) // Kotlin：数组用 intArrayOf(...) 初始化
    Solution().duplicateZeros(arr)
    println(arr.joinToString(",")) // 期望：1,0,0,2,3,0,0,4
}
```
