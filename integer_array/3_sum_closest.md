# 3 Sum Closest

Tags: Array, Hash Table, Mediam, Facebook, Google, Uber

## Question

- leetcode: [3Sum Closest | LeetCode OJ](https://leetcode.com/problems/3sum-closest/)
- lintcode: [(59) 3 Sum Closest](http://www.lintcode.com/en/problem/3-sum-closest/)

```
Given an array S of n integers, find three integers in S such that the sum is closest to a given number, target. 
Return the sum of the three integers. You may assume that each input would have exactly one solution.

For example, given array S = {-1 2 1 -4}, and target = 1.
The sum that is closest to the target is 2. (-1 + 2 + 1 = 2).
```

### 分析

先排序，然后左右夹逼，复杂度 $$O(n^2)$$。


### 代码

```java
// 3Sum Closest
// 先排序，然后左右夹逼
// Time Complexity: O(n^2), Space Complexity: O(1)
public class Solution {
    public int threeSumClosest(int[] nums, int target) {
        int result = 0;
        int minGap = Integer.MAX_VALUE;
        Arrays.sort(nums);

        for (int i = 0; i < nums.length - 1; ++i) {
            int j = i + 1;
            int k = nums.length - 1;

            while(j < k) {
                final int sum = nums[i] + nums[j] + nums[k];
                final int gap = Math.abs(sum - target);
                if (gap < minGap) {
                    result = sum;
                    minGap = gap;
                }

                if (sum < target) ++j;
                else              --k;
            }
        }
        return result;
    }
}

## 题解1 - 排序 + 2 Sum + 两根指针 + 优化过滤

和 3 Sum 的思路接近，首先对原数组排序，随后将3 Sum 的题拆解为『1 Sum + 2 Sum』的题，对于 Closest 的题使用两根指针而不是哈希表的方法较为方便。对于有序数组来说，在查找 Cloest 的值时其实是有较大的优化空间的。

### 源码分析

和前面3Sum解法相似，同理使用i,j,k三个指针进行循环。<br>
区别在于3sum中的target为0，这里新增一个变量用于比较哪组数据与target更为相近

### 复杂度分析

时间复杂度同理为$$O(n^2)$$
运行时间 16ms

## Reference

- [3Sum Closest | 九章算法](http://www.jiuzhang.com/solutions/3sum-closest/)
