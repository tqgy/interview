# Maximum Subarray

## Question

- leetcode: [Maximum Subarray | LeetCode OJ](https://leetcode.com/problems/maximum-subarray/)
- lintcode: [(41) Maximum Subarray](http://www.lintcode.com/en/problem/maximum-subarray/)

```
Given an array of integers,
find a contiguous subarray which has the largest sum.

Example
Given the array [−2,2,−3,4,−1,2,1,−5,3],
the contiguous subarray [4,−1,2,1] has the largest sum = 6.

Note
The subarray should contain at least one number.

Challenge
Can you do it in time complexity O(n)?
```

## 题解1 - 贪心

求最大子数组和，即求区间和的最大值，不同子区间共有约 $$n^2$$ 中可能，遍历虽然可解，但是时间复杂度颇高。

这里首先介绍一种巧妙的贪心算法，用`sum`表示当前子数组和，`maxSum`表示求得的最大子数组和。当`sum <= 0`时，累加数组中的元素只会使得到的和更小，故此时应将此部分和丢弃，使用此时遍历到的数组元素替代。需要注意的是由于有`maxSum`更新`sum`, 故直接丢弃小于0的`sum`并不会对最终结果有影响。即不会漏掉前面的和比后面的元素大的情况。

### Java

```java
public class Solution {
    /**
     * @param nums: A list of integers
     * @return: A integer indicate the sum of max subarray
     */
    public int maxSubArray(ArrayList<Integer> nums) {
        // -1 is not proper for illegal input
        if (nums == null || nums.isEmpty()) return -1;

        int sum = 0, maxSub = Integer.MIN_VALUE;
        for (int num : nums) {
            // drop negtive sum
            sum = Math.max(sum, 0);
            sum += num;
            // update maxSub
            maxSub = Math.max(maxSub, sum);
        }

        return maxSub;
    }
}
```

### 源码分析

贪心的实现较为巧妙，需要`sum`和`maxSub`配合运作才能正常工作。

### 复杂度分析

遍历一次数组，时间复杂度 $$O(n)$$, 使用了几个额外变量，空间复杂度 $$O(1)$$.

## 题解2 - 动态规划1(区间和)

求最大/最小这种字眼往往都可以使用动态规划求解，此题为单序列动态规划。我们可以先求出到索引 i 的子数组和，然后用子数组和的最大值减去最小值，最后返回最大值即可。用这种动态规划需要注意初始化条件和求和顺序。

### Java

```java
public class Solution {
    /**
     * @param nums: A list of integers
     * @return: A integer indicate the sum of max subarray
     */
    public int maxSubArray(ArrayList<Integer> nums) {
        // -1 is not proper for illegal input
        if (nums == null || nums.isEmpty()) return -1;

        int sum = 0, minSum = 0, maxSub = Integer.MIN_VALUE;
        for (int num : nums) {
            minSum = Math.min(minSum, sum);
            sum += num;
            maxSub = Math.max(maxSub, sum - minSum);
        }

        return maxSub;
    }
}
```

### 源码分析

首先求得当前的最小子数组和，初始化为0，随后比较子数组和减掉最小子数组和的差值和最大区间和，并更新最大区间和。

### 复杂度分析

时间复杂度 $$O(n)$$, 使用了类似滚动数组的处理方式，空间复杂度 $$O(1)$$.

## 题解3 - 动态规划2(局部与全局)

这种动规的实现和题解1 的思想几乎一模一样，只不过这里用局部最大值和全局最大值两个数组来表示。

### Java

```java
public class Solution {
    /**
     * @param nums: A list of integers
     * @return: A integer indicate the sum of max subarray
     */
    public int maxSubArray(ArrayList<Integer> nums) {
        // -1 is not proper for illegal input
        if (nums == null || nums.isEmpty()) return -1;

        int size = nums.size();
        int[] local = new int[size];
        int[] global = new int[size];
        local[0] = nums.get(0);
        global[0] = nums.get(0);
        for (int i = 1; i < size; i++) {
            // drop local[i - 1] < 0
            local[i] = Math.max(nums.get(i), local[i - 1] + nums.get(i));
            // update global with local
            global[i] = Math.max(global[i - 1], local[i]);
        }

        return global[size - 1];
    }
}
```

### 源码分析

由于局部最大值需要根据之前的局部值是否大于0进行更新，故方便起见初始化 local 和 global 数组的第一个元素为数组第一个元素。

### 复杂度分析

时间复杂度 $$O(n)$$, 空间复杂度也为 $$O(n)$$.

### 分析

最大连续子序列和，非常经典的题。

当我们从头到尾遍历这个数组的时候，对于数组里的一个整数，它有几种选择呢？它只有两种选择： 1、加入之前的SubArray；2. 自己另起一个SubArray。那什么时候会出现这两种情况呢？

如果之前SubArray的总体和大于0的话，我们认为其对后续结果是有贡献的。这种情况下我们选择加入之前的SubArray

如果之前SubArray的总体和为0或者小于0的话，我们认为其对后续结果是没有贡献，甚至是有害的（小于0时）。这种情况下我们选择以这个数字开始，另起一个SubArray。

设状态为`f[j]`，表示以`S[j]`结尾的最大连续子序列和，则状态转移方程如下：

$$f[j] = \max\left\{f[j-1]+S[j],S[j]\right\}, 1 \leq j \leq n$$

$$target = \max\left\{f[j]\right\}, 1 \leq j \leq n$$

解释如下：

* 情况一，S[j]不独立，与前面的某些数组成一个连续子序列，则最大连续子序列和为`f[j-1]+S[j]`。
* 情况二，S[j]独立划分成为一段，即连续子序列仅包含一个数S[j]，则最大连续子序列和为`S[j]`。  

其他思路：

* 思路2：直接在i到j之间暴力枚举，复杂度是`O(n^3)`
* 思路3：处理后枚举，连续子序列的和等于两个前缀和之差，复杂度`O(n^2)`。
* 思路4：分治法，把序列分为两段，分别求最大连续子序列和，然后归并，复杂度`O(nlog n)`
* 思路5：把思路2`O(n^2)`的代码稍作处理，得到`O(n)`的算法
* 思路6：当成M=1的最大M子段和


### 动规

{% codesnippet "./code/maximum-subarray-1."+book.suffix, language=book.suffix %}{% endcodesnippet %}

### 思路5

{% if book.java %}
```java
// Maximum Subarray
// 时间复杂度O(n)，空间复杂度O(n)
public class Solution {
    public int maxSubArray(int[] nums) {
        return mcss(nums, 0, nums.length);
    }
    // 思路5，求最大连续子序列和
    private static int mcss(int[] nums, int begin, int end) {
        final int n = end - begin;
        int[] sum = new int[n + 1];  // 前n项和

        int result = Integer.MIN_VALUE;
        int cur_min = sum[0];
        for (int i = 1; i <= n; i++) {
            sum[i] = sum[i - 1] + nums[begin  + i - 1];
        }
        for (int i = 1; i <= n; i++) {
            result = Math.max(result, sum[i] - cur_min);
            cur_min = Math.min(cur_min, sum[i]);
        }
        return result;
    }
}
```

## Reference

- 《剑指 Offer》第五章
- [Maximum Subarray 参考程序 Java/C++/Python](http://www.jiuzhang.com/solutions/maximum-subarray/)
