#15.[Mediam] 3 Sum

Tags: Array, Hash Table, Mediam, Facebook, Google, Uber

## Question

- leetcode: [3Sum | LeetCode OJ](https://leetcode.com/problems/3sum/)
- lintcode: [(57) 3 Sum](http://www.lintcode.com/en/problem/3sum/)

### Problem Statement

Given an array _S_ of _n_ integers, are there elements _a_, _b_, _c_ in _S_
such that _a_ + _b_ + _c_ = 0? Find all unique triplets in the array which
gives the sum of zero.

#### Note:

The solution set must not contain duplicate triplets.
    
    For example, given array S = [-1, 0, 1, 2, -1, -4],
    
    A solution set is:
    [
      [-1, 0, 1],
      [-1, -1, 2]
    ]


## 题解1 - 排序 + 哈希表 + 2 Sum

相比之前的 [2 Sum] 3 Sum 又多加了一个数，按照之前 2 Sum 的分解为『1 Sum + 1 Sum』的思路，我们同样可以将 3 Sum 分解为『1 Sum + 2 Sum』的问题，具体就是首先对原数组排序，排序后选出第一个元素，随后在剩下的元素中使用 2 Sum 的解法。

### 源码分析

1. 异常处理，对长度小于3的直接返回。
2. 排序输入数组，有助于提高效率和返回有序列表。
3. 循环遍历排序后数组，先取出一个元素，随后求得 2 Sum 中需要的目标数。
4. 由于本题中最后返回结果不能重复，在加入到最终返回值之前查重。

由于排序后的元素已经按照大小顺序排列，且在2 Sum 中先遍历的元素较小，所以无需对列表内元素再排序。

### 复杂度分析

排序时间复杂度 $$O(n \log n)$$, 两重`for`循环，时间复杂度近似为 $$O(n^2)$$，使用哈希表(字典)实现，空间复杂度为 $$O(n)$$.

目前这段源码为比较简易的实现，leetcode 上的运行时间为500 + ms, 还有较大的优化空间，嗯，后续再进行优化。

### Java
```java
public class Solution {
    public List<List<Integer>> threeSum(int[] nums) {
        // Assumptions: array is not null, array.length >= 3
        List<List<Integer>> result = new ArrayList<List<Integer>>();
        Arrays.sort(nums);
        for (int i = 0; i < nums.length - 2; i++) {
            if (i > 0 && nums[i] == nums[i - 1]) {
                continue;
            }
            int left = i + 1;
            int right = nums.length - 1;
            while (left < right) {
                int tmp = nums[left] + nums[right];
                if (tmp + nums[i] == 0) {
                    result.add(Arrays.asList(nums[i], nums[left], nums[right]));
                    left++;
                    while (left < right && nums[left] == nums[left - 1]) {
                        left++;
                    }
                } else if (tmp + nums[i] < 0) {
                    left++;
                } else {
                    right--;
                }
            }
        }
        return result;
    }
}
```

###源码分析

同python解法不同，没有使用hash map
```
S = {-1 0 1 2 -1 -4}
排序后：
S = {-4 -1 -1 0 1 2}
      ↑  ↑        ↑
      i  j        k
         →        ←
i每轮只走一步，j和k根据S[i]+S[j]+S[k]=ans和0的关系进行移动，且j只向后走（即S[j]只增大），k只向前走（即S[k]只减小）
如果ans>0说明S[k]过大，k向前移；如果ans<0说明S[j]过小，j向后移；ans==0即为所求。
至于如何取到所有解，看代码即可理解，不再赘述。
```

### 复杂度分析

外循环i走了n轮,每轮j和k一共走n-i步，所以时间复杂度为$$O(n^2)$$。
最终运行时间为52ms


### 分析

先排序，然后左右夹逼，复杂度 $$O(n^2)$$。

这个方法可以推广到`k-sum`，先排序，然后做`k-2`次循环，在最内层循环左右夹逼，时间复杂度是 $$O(\max\{n \log n, n^{k-1}\})$$。


### 代码

```java
// 3Sum
// 先排序，然后左右夹逼，注意跳过重复的数
// Time Complexity: O(n^2)，Space Complexity: O(1)
public class Solution {
    public List<List<Integer>> threeSum(int[] nums) {
        List<List<Integer>> result = new ArrayList<>();
        if (nums.length < 3) return result;
        Arrays.sort(nums);
        final int target = 0;
        
        for (int i = 0; i < nums.length - 2; ++i) {
            if (i > 0 && nums[i] == nums[i-1]) continue;
            int j = i+1;
            int k = nums.length-1;
            while (j < k) {
                if (nums[i] + nums[j] + nums[k] < target) {
                    ++j;
                    while(nums[j] == nums[j-1] && j < k) ++j;
                } else if(nums[i] + nums[j] + nums[k] > target) {
                    --k;
                    while(nums[k] == nums[k+1] && j < k) --k;
                } else {
                    result.add(Arrays.asList(nums[i], nums[j], nums[k]));
                    ++j;
                    --k;
                    while(nums[j] == nums[j-1] && j < k) ++j;
                    while(nums[k] == nums[k+1] && j < k) --k;
                }
            }
        }
        return result;
    }
};
```

## Reference

- [3Sum | 九章算法](http://www.jiuzhang.com/solutions/3sum/)
- [A simply Python version based on 2sum - O(n^2) - Leetcode Discuss](https://leetcode.com/discuss/32455/a-simply-python-version-based-on-2sum-o-n-2)
