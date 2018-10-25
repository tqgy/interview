# Next Permutation

## Question

- leetcode: [Next Permutation | LeetCode OJ](https://leetcode.com/problems/next-permutation/)
- lintcode: [(52) Next Permutation](http://www.lintcode.com/en/problem/next-permutation/)

### Problem Statement

Given a list of integers, which denote a permutation.

Find the next permutation in ascending order.

#### Example

For `[1,3,2,3]`, the next permutation is `[1,3,3,2]`

For `[4,3,2,1]`, the next permutation is `[1,2,3,4]`

#### Note

The list may contains duplicate integers.


## 题解

找下一个升序排列，C++ STL 源码剖析一书中有提及，[Permutations](http://algorithm.yuanbin.me/zh-hans/exhaustive_search/permutations.html) 一小节中也有详细介绍，下面简要介绍一下字典序算法：

1. 从后往前寻找索引满足 `a[k] < a[k + 1]`, 如果此条件不满足，则说明已遍历到最后一个。
2. 从后往前遍历，找到第一个比`a[k]`大的数`a[l]`, 即`a[k] < a[l]`.
3. 交换`a[k]`与`a[l]`.
4. 反转`k + 1 ~ n`之间的元素。

由于这道题中规定对于`[4,3,2,1]`, 输出为`[1,2,3,4]`, 故在第一步稍加处理即可。

### Java

```java
public class Solution {
    /**
     * @param nums: an array of integers
     * @return: return nothing (void), do not return anything, modify nums in-place instead
     */
    public void nextPermutation(int[] nums) {
        if (nums == null || nums.length == 0) return;

        // step1: search the first nums[k] < nums[k+1] backward
        int k = -1;
        for (int i = nums.length - 2; i >= 0; i--) {
            if (nums[i] < nums[i + 1]) {
                k = i;
                break;
            }
        }
        // if current rank is the largest, reverse it to smallest, return
        if (k == -1) {
            reverse(nums, 0, nums.length - 1);
            return;
        }

        // step2: search the first nums[k] < nums[l] backward
        int l = nums.length - 1;
        while (l > k && nums[l] <= nums[k]) l--;

        // step3: swap nums[k] with nums[l]
        int temp = nums[k];
        nums[k] = nums[l];
        nums[l] = temp;

        // step4: reverse between k+1 and nums.length-1;
        reverse(nums, k + 1, nums.length - 1);
    }

    private void reverse(int[] nums, int lb, int ub) {
        for (int i = lb, j = ub; i < j; i++, j--) {
            int temp = nums[i];
            nums[i] = nums[j];
            nums[j] = temp;
        }
    }
}
```

### 源码分析

和 Permutation 一小节类似，这里只需要注意在step 1中`i == -1`时需要反转之以获得最小的序列。对于有重复元素，只要在 step1和 step2中判断元素大小时不取等号即可。Lintcode 上给的注释要求（其实是 Leetcode 上的要求）和实际给出的输出不一样。

### 复杂度分析

最坏情况下，遍历两次原数组，反转一次数组，时间复杂度为 $$O(n)$$, 使用了 temp 临时变量，空间复杂度可认为是 $$O(1)$$.
